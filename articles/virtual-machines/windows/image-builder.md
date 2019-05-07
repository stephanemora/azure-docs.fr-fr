---
title: Créer une machine virtuelle Windows avec le Générateur d’images Azure (version préliminaire)
description: Créer une machine virtuelle Windows avec le Générateur d’images Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: 01109aa83c12bda9b1d21ec25784d663f8abf700
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159719"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Aperçu : Créer une machine virtuelle Windows avec le Générateur d’images Azure

Cet article est de vous montrer comment vous pouvez créer une image Windows personnalisée à l’aide du Générateur d’images de machine virtuelle Azure. L’exemple de cet article utilise trois différents [personnalisateurs](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) pour la personnalisation de l’image :
- PowerShell (ScriptUri) - télécharger et exécuter un [script PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Redémarrage de Windows - redémarre la machine virtuelle.
- PowerShell (inline) - exécuter une commande spécifique. Dans cet exemple, il crée un répertoire sur la machine virtuelle à l’aide `mkdir c:\\buildActions`.
- Fichier - copier un fichier à partir de GitHub à la machine virtuelle. Cet exemple copie [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) à `c:\buildArtifacts\index.html` sur la machine virtuelle.

Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier .json que nous utilisons est ici : [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Générateur d’images Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Inscrire les fonctionnalités

Pour utiliser le Générateur d’images Azure durant la phase préliminaire, vous devez inscrire la nouvelle fonctionnalité.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Vérifiez l’état de l’inscription de fonctionnalité.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Vérifier votre inscription.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Si elles ne dites pas inscrit, exécutez la commande suivante :

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour stocker ces informations.

```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Créer une variable pour votre ID d’abonnement. Vous pouvez obtenir à l’aide de cette `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Créez le groupe de ressources.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Accorder l’autorisation d’Image Builder pour créer des ressources dans ce groupe de ressources. Le `--assignee` valeur est l’ID d’inscription d’application pour le service de générateur d’images. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Téléchargez l’exemple .json

Télécharger l’exemple de fichier .json et configurez-le avec les variables que vous avez créé.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Création de l’image

Envoyer la configuration de l’image pour le service de générateur d’images de machine virtuelle

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Démarrer la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Attendez que la build est terminée. Cela peut prendre environ 15 minutes.

## <a name="create-the-vm"></a>Création de la machine virtuelle

Créer la machine virtuelle à l’aide de l’image que vous avez créé. Remplacez *<password>* avec votre mot de passe pour le `aibuser` sur la machine virtuelle.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Vérifier la personnalisation

Créer une connexion Bureau à distance à la machine virtuelle en utilisant le nom d’utilisateur et le mot de passe définis lors de la création de la machine virtuelle. À l’intérieur de la machine virtuelle, ouvrez une invite de commande et tapez :

```console
dir c:\
```

Vous devez voir ces deux répertoires créés au cours de personnalisation de l’image :
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Nettoyer

Lorsque vous avez terminé, supprimez les ressources.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les composants du fichier .json utilisé dans cet article, consultez [référence du modèle Générateur d’Image](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

