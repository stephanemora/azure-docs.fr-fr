---
title: Créer une machine virtuelle à partir d’une version d’image spécialisée à l’aide d’Azure CLI
description: Créez une machine virtuelle à l’aide d’une version d’image spécialisée dans une galerie d’images partagées à l’aide d’Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: a22c0941a405dd2fcf37403e3a12113bc51cb774
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005801"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Créer une machine virtuelle à l’aide d’une version d’image spécialisée avec Azure CLI

Créez une machine virtuelle à partir d’une [version d’image spécialisée](./linux/shared-image-galleries.md#generalized-and-specialized-images) stockée dans une galerie d’images partagées. Si vous souhaitez créer une machine virtuelle à l’aide d’une version d’image généralisée, consultez [Créer une machine virtuelle à partir d’une version d’image généralisée](vm-generalized-image-version-cli.md).

Remplacez les noms de ressources en fonction des besoins dans cet exemple. 

Répertoriez les définitions d’images d’une galerie avec la commande [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) pour voir le nom et l’ID des définitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Créez la machine virtuelle avec la commande [az vm create](/cli/azure/vm#az-vm-create), en spécifiant le paramètre --specialized pour indiquer que l’image est une image spécialisée. 

Utilisez l’ID de définition d’image pour `--image` afin de créer la machine virtuelle à partir de la version la plus récente de l’image disponible. Vous pouvez également créer la machine virtuelle à partir d’une version spécifique en fournissant l’ID de version de l’image pour `--image`. 

Dans cet exemple, nous créons une machine virtuelle à partir de la dernière version de l’image *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Étapes suivantes
[Générateur d’image Azure (préversion)](./linux/image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./linux/image-builder-gallery-update-image-version.md). 

Vous pouvez également créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Créer une machine virtuelle à partir d’une version d’image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)
