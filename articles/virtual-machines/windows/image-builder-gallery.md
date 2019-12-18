---
title: Utiliser le Générateur d’images Azure avec une galerie d’images pour machines virtuelles Windows (préversion)
description: Créez des images pour machines virtuelles Windows avec le Générateur d’images Azure et Shared Image Gallery.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 1d9763ccc5f5967b9fc9932a11fff655e6120fd0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976075"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Aperçu : Créer une image Windows et la distribuer sur une bibliothèque d’images partagées 

Cet article explique comment utiliser le Générateur d’images Azure pour créer une version d’une image dans une [Bibliothèque d’images partagées](shared-image-galleries.md), puis distribuer l’image dans le monde entier.

Nous utiliserons un modèle .json.pour configurer l’image. Le fichier en question se trouve à l’emplacement [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Pour distribuer l’image sur une bibliothèque d’images partagées, le modèle utilise [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) comme valeur de la section `distribute` du modèle.

> [!IMPORTANT]
> Le Générateur d’images Azure est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Inscrire les fonctionnalités
Pour utiliser le Générateur d’images Azure pendant la préversion, vous devez inscrire la nouvelle fonctionnalité.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Vérifiez l’état d’inscription de la fonctionnalité.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Vérifiez votre inscription.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
```

Si elle n’est pas inscrite, exécutez la commande suivante :

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Définir des variables et des autorisations 

Comme certaines informations seront utilisées plusieurs fois, nous allons créer des variables pour les stocker. Remplacez la valeur des variables, comme `username` et `vmpassword`, par vos propres informations.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Créez une variable pour votre ID d’abonnement, Vous pouvez l’obtenir avec `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Créez le groupe de ressources.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Accordez au Générateur d’images Azure l’autorisation de créer des ressources dans ce groupe de ressources. La valeur `--assignee` est l’ID d’inscription d’application du service Générateur d’images. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Créer une définition d’image et une galerie

Pour utiliser Image Builder avec une galerie d’images partagées, vous devez disposer d’une bibliothèque d’images et d’une définition d’image existantes. Image Builder ne va pas créer la Galerie d’images et la définition d’image pour vous.

Si vous n’avez pas encore de définition d’image et de galerie à utiliser, commencez par les créer. Tout d’abord, créez une galerie d’images.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Ensuite, créez une définition d’image.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Télécharger et configurer le fichier .json

Téléchargez le modèle .json et configurez-le avec vos variables.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Créer la version de l’image

Cette section a pour objectif de créer la version de l’image dans la galerie. 

Envoyez la configuration de l’image au service Générateur d’images Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Lancez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

La création de l’image et sa réplication dans les deux régions peuvent prendre un certain temps. Attendez la fin de cette partie pour passer à la création d’une machine virtuelle.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez une machine virtuelle à partir de la version de l’image créée par le Générateur d’images Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Vérifier la personnalisation
Créez une connexion Bureau à distance à la machine virtuelle avec le nom d’utilisateur et le mot de passe définis lors de la création de la machine virtuelle. Dans la machine virtuelle, ouvrez une invite de commande et tapez :

```console
dir c:\
```

Le répertoire nommé `buildActions`, créé pendant la personnalisation de l’image, devrait apparaître.


## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous souhaitez maintenant essayer de repersonnaliser la version de l’image pour créer une nouvelle version de la même image, **ignorez cette étape** et passez à [Utiliser le Générateur d’images Azure pour créer une autre version de l’image](image-builder-gallery-update-image-version.md).


L’image créée ainsi que tous les autres fichiers de ressources seront ainsi supprimés. Terminez ce déploiement avant de supprimer les ressources.

En ce qui concerne la suppression des ressources de la bibliothèque d’images, il est nécessaire de supprimer toutes les versions de l’image pour pouvoir supprimer la définition de l’image utilisée pour les créer. Supprimer une galerie implique de supprimer au préalable toutes les définitions de l’image qu’elle comporte.

Supprimez le modèle du Générateur d’images.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Récupérez la version de l’image créée par le Générateur d’images, qui commence toujours par `0.`, puis supprimez la version de l’image

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Supprimez la définition de l’image.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Supprimez la galerie.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Supprimez le groupe de ressources.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment mettre à jour la version de l’image créée, voir [Utiliser le Générateur d’images Azure pour créer une autre version de l’image](image-builder-gallery-update-image-version.md).