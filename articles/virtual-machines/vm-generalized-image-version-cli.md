---
title: Créer une machine virtuelle à partir d’une image généralisée à l’aide d’Azure CLI
description: Créez une machine virtuelle à partir d’une version d’image généralisée à l’aide d’Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9304f75c1de6a46da960187ef82951bc77df4d96
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688159"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Créer une machine virtuelle à partir d’une version d’image généralisée en utilisant Azure CLI

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles 

Créez une machine virtuelle à partir d’une [version d’image généralisée](./shared-image-galleries.md#generalized-and-specialized-images) stockée dans une galerie d’images partagées. Si vous souhaitez créer une machine virtuelle en utilisant une image spécialisée, consultez [Créer une machine virtuelle à partir d’une image spécialisée](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Obtenir l’ID de l’image

Répertoriez les définitions d’images d’une galerie avec la commande [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) pour voir le nom et l’ID des définitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm#az_vm_create). Pour utiliser la version la plus récente de l’image, définissez `--image` sur l’ID de la définition d’image. 

Remplacez les noms de ressources en fonction des besoins dans cet exemple. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Vous pouvez également utiliser une version spécifique en utilisant l’ID de version de l’image pour le paramètre `--image`. Par exemple, pour utiliser la version de l’image *1.0.0* tapez : `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

## <a name="next-steps"></a>Étapes suivantes

[Générateur d’image Azure (préversion)](./image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./linux/image-builder-gallery-update-image-version.md).