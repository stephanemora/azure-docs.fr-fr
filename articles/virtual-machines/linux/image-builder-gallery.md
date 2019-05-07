---
title: Utiliser le Générateur d’images Azure avec une galerie d’images pour les machines virtuelles Linux (version préliminaire)
description: Créer des images Linux avec le Générateur d’images Azure et de la galerie d’images partagé.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e9a8a30d9f5f170073c0ad671a248703b1078864
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159494"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Aperçu : Créer une image Linux et les distribuer à une galerie d’images partagé 

Cet article vous montre comment vous pouvez utiliser le Générateur d’images Azure pour créer une version de l’image dans un [Galerie d’images partagé](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/shared-image-galleries), puis distribuer l’image dans le monde entier.


Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier .json que nous utilisons est ici : [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Pour distribuer l’image à une galerie d’images partagé, le modèle utilise [sharedImage](image-builder-json.md#distribute-sharedimage) comme valeur pour le `distribute` section du modèle.

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

## <a name="set-variables-and-permissions"></a>Définition des variables et les autorisations 

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour stocker ces informations.

Pour la version préliminaire, Générateur d’images est uniquement en charge la création d’images personnalisées dans le même groupe de ressources comme image source managé. Mettre à jour le nom du groupe de ressources dans cet exemple pour être le même groupe de ressources que votre image managée source.

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

Créer une variable pour votre ID d’abonnement. Vous pouvez obtenir à l’aide de cette `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Créez le groupe de ressources.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Accorder l’autorisation de générateur d’images Azure pour créer des ressources dans ce groupe de ressources. Le `--assignee` valeur est l’ID d’inscription d’application pour le service de générateur d’images. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Créer une définition de l’image et de la galerie

Créer une galerie d’images. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Créer une définition de l’image.

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


## <a name="download-and-configure-the-json"></a>Télécharger et configurer le .json

Télécharger le modèle .json et configurez-le avec vos variables.

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

La section suivante crée la version de l’image dans la galerie. 

Envoyer la configuration de l’image pour le service de générateur d’images Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Démarrer la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Création de l’image et la réplique sur les deux régions peuvent prendre un certain temps. Attendre la fin de cette partie avant de passer à la création d’une machine virtuelle.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créer une machine virtuelle à partir de la version de l’image qui a été créée par le Générateur d’images Azure.

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

Vous devez voir l’image a été personnalisé avec un *Message de la journée* dès que votre connexion SSH est établie !

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez maintenant essayer de ré-personnalisation de la version de l’image pour créer une nouvelle version de la même image, ignorez les étapes et passez à [utilisez Azure Image Builder pour créer une autre version de l’image](image-builder-gallery-update-image-version.md).


Cette opération supprimera l’image qui a été créé, ainsi que tous les autres fichiers de ressources. Assurez-vous que vous avez terminé avec ce déploiement avant de supprimer les ressources.

Lors de la suppression des ressources de galerie d’images, vous devez supprimer toutes les versions de l’image avant de pouvoir supprimer la définition de l’image utilisée pour les créer. Pour supprimer une galerie, vous devez d’abord avoir supprimé toutes les définitions de l’image dans la galerie.

Supprimer le modèle de générateur d’image.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Obtenir la version de l’image créée par le Générateur d’images, il commence toujours par `0.`, puis supprimez la version de l’image

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


Supprimer la définition de l’image.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Supprimer la galerie.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Supprimez le groupe de ressources.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [galeries d’images Azure Shared](shared-image-galleries.md).