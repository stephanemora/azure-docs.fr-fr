---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 830deb7569772b610b7e6abde649830b7ad67a92
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046083"
---
## <a name="before-you-begin"></a>Avant de commencer

Pour suivre l’exemple de cet article, vous devez avoir l’image managée d’une machine virtuelle généralisée. Pour plus d’informations, consultez [Tutoriel : Créer une image personnalisée d’une machine virtuelle Azure avec Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Préversion : Inscrire la fonctionnalité

La galerie d’images partagées est en préversion, mais vous devez l’inscrire pour pouvoir l’utiliser. Pour inscrire la fonctionnalité de galerie d’images partagées :

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

L’inscription de la fonctionnalité peut prendre quelques minutes. Vous pouvez vérifier la progression avec la commande :

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>Créer une galerie d’images 

Une galerie d’images est la ressource principale utilisée pour activer le partage d’image. Les noms de galerie doivent être uniques dans votre abonnement. Créez une galerie d’images à l’aide de [az sig create](/cli/azure/sig#az-sig-create). L’exemple suivant crée une galerie nommée *myGallery* dans *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Créer une définition d’image

Créer une définition d’image initiale dans la galerie avec la commande [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>Créer une version d’image 
 
Créez des versions d’image en fonction de vos besoins avec la commande [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). L’ID de l’image managée sert de base de référence pour la création de la version d’image. Vous pouvez utiliser [az image list](/cli/azure/image?view#az-image-list) pour obtenir des informations sur les images qui se trouvent dans un groupe de ressources. Dans cet exemple, la version de notre image est *1.0.0* et nous allons créer 5 réplicas au total dans les régions *USA Centre-Ouest*, *USA Centre Sud* et USA Est 2*.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

