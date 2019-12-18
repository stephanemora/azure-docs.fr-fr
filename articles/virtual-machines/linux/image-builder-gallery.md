---
title: Utiliser le Générateur d’images Azure avec une galerie d’images pour machines virtuelles Linux (préversion)
description: Créez des images de machines virtuelles Linux avec le Générateur d’images Azure et Shared Image Gallery.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 09dceb84a20ef49b3e9d5264b94bb5e74180cd2b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976126"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Aperçu : Créer une image Linux et la distribuer à une galerie d’images partagées 

Cet article explique comment utiliser le Générateur d’images Azure pour créer une version d’une image dans une [galerie d’images partagées](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), puis distribuer l’image dans le monde entier.


Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier en question se trouve à l’emplacement [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Pour distribuer l’image à une galerie d’images partagées, le modèle utilise [sharedImage](image-builder-json.md#distribute-sharedimage) comme valeur de la section `distribute` du modèle.

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
```

Si elle n’est pas inscrite, exécutez la commande suivante :

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Définir des variables et des autorisations 

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour les stocker.

Dans la préversion, le Générateur d’images ne prend en charge la création d’images personnalisées que dans le même groupe de ressources que l’image managée source. Remplacez le nom du groupe de ressources de cet exemple par celui de votre image managée source.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Créez une variable pour votre ID d’abonnement. Vous pouvez l’obtenir avec `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
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
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Télécharger et configurer le fichier .json

Téléchargez le modèle .json et configurez-le avec vos variables.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Créer la version de l’image

Cette section a pour objectif de créer la version de l’image dans la galerie. 

Envoyez la configuration de l’image au service Générateur d’images Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Lancez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

La création de l’image et sa réplication dans les deux régions peuvent prendre un certain temps. Attendez la fin de cette partie pour passer à la création d’une machine virtuelle.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez une machine virtuelle à partir de la version de l’image créée par le Générateur d’images Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Connectez-vous avec SSH à la machine virtuelle.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

L’image est personnalisée avec un *Message du jour* dès que la connexion SSH est établie.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez maintenant essayer de personnaliser à nouveau la version de l’image pour créer une nouvelle version de la même image, ignorez les étapes suivantes et passez à [Utiliser le Générateur d’images Azure pour créer une autre version de l’image](image-builder-gallery-update-image-version.md).


L’image créée ainsi que tous les autres fichiers de ressources seront ainsi supprimés. Terminez ce déploiement avant de supprimer les ressources.

En ce qui concerne la suppression des ressources de la bibliothèque d’images, il est nécessaire de supprimer toutes les versions de l’image pour pouvoir supprimer la définition de l’image utilisée pour les créer. Supprimer une galerie implique de supprimer au préalable toutes les définitions de l’image qu’elle comporte.

Supprimez le modèle du Générateur d’images.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
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

En savoir plus sur les [galeries d’images partagées Azure](shared-image-galleries.md).