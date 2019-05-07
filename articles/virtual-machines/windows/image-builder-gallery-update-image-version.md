---
title: Créer une nouvelle version de l’image à partir d’une version existante de l’image à l’aide du Générateur d’images Azure (version préliminaire)
description: Créer une nouvelle version de l’image à partir d’une version existante de l’image à l’aide du Générateur d’images Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: f1bce4c2e5c7ae9dc7ec5917fbc5ac115eecdffa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160094"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Aperçu : Créer une nouvelle version de l’image à partir d’une version existante de l’image à l’aide du Générateur d’images Azure

Cet article vous montre comment prendre une version existante de l’image un [Galerie d’images partagé](shared-image-galleries.md), mettez-le à jour et le publier en tant qu’une nouvelle version de l’image dans la galerie.

Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier .json que nous utilisons est ici : [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Si elles ne dites pas inscrit, exécutez la commande suivante :

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Définition des variables et les autorisations

Si vous avez utilisé [créer une image et la distribuer à une galerie d’images partagé](image-builder-gallery.md) pour créer votre galerie d’images partagé, vous avez déjà créé les variables que nous avons besoin. Si ce n’est pas le cas, veuillez configurer certaines variables à utiliser pour cet exemple.

Pour la version préliminaire, Générateur d’images est uniquement en charge la création d’images personnalisées dans le même groupe de ressources comme image source managé. Mettre à jour le nom du groupe de ressources dans cet exemple pour être le même groupe de ressources que votre image managée source.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Créer une variable pour votre ID d’abonnement. Vous pouvez obtenir à l’aide de cette `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Obtenir la version de l’image que vous souhaitez mettre à jour.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Si vous avez déjà votre propre galerie d’images partagé et n’avez pas suivi de l’exemple précédent, vous devez affecter des autorisations pour le Générateur d’images accéder au groupe de ressources, donc il peut accéder à la galerie.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modifier l’exemple de helloImage
Vous pouvez consulter l’exemple que nous sommes sur le point d’utiliser en ouvrant le fichier .json ici : [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) avec la [référence du modèle Générateur d’images](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Téléchargez l’exemple .json et configurez-le avec vos variables. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Création de l’image

Envoyer la configuration de l’image pour le Service de génération d’Image de machine virtuelle.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Démarrer la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Attendez que l’image a été créée et la réplication avant de passer à l’étape suivante.


## <a name="create-the-vm"></a>Création de la machine virtuelle

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Vérifier la personnalisation
Créer une connexion Bureau à distance à la machine virtuelle en utilisant le nom d’utilisateur et le mot de passe définis lors de la création de la machine virtuelle. À l’intérieur de la machine virtuelle, ouvrez une invite de commande et tapez :

```console
dir c:\
```

Vous devriez maintenant voir deux répertoires :
- `buildActions` qui a été créé dans la première version de l’image.
- `buildActions2` qui a été créé dans le cadre de la mise à jour de la première version d’image pour créer la deuxième version de l’image.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les composants du fichier .json utilisé dans cet article, consultez [référence du modèle Générateur d’Image](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).