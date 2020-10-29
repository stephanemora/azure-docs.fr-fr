---
title: Créer un disque géré à partir d’une version d’image
description: Créez un disque géré à partir d’une version d’image dans une galerie d’images partagées.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 35edcfb4bdb0715245f4a3190fb22638b1162429
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370982"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Créer un disque géré à partir d’une version d’image

Si nécessaire, vous pouvez créer un disque géré à partir d’une version d’image stockée dans une galerie d’images partagées.


## <a name="cli"></a>Interface de ligne de commande

Affectez à la variable `source` l’ID de la version de l’image, puis utilisez [az disk create](/cli/azure/disk#az_disk_create) pour créer le disque géré. 


Vous pouvez voir une liste de versions d’images à l’aide de [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list). Dans cet exemple, nous recherchons toutes les versions d’image qui font partie de la définition d’image *myImageDefinition* dans la galerie d’images *myGallery* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


Dans cet exemple, nous créons un disque géré nommé *myManagedDisk* , dans la région *EastUS* , dans un groupe de ressources nommé *myResourceGroup* . 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Si le disque est un disque de données, ajoutez `--gallery-image-reference-lun` pour spécifier le numéro d’unité logique.

## <a name="powershell"></a>PowerShell

Vous pouvez répertorier toutes les versions de l’image avec [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



Une fois que vous disposez de toutes les informations dont vous avez besoin, vous pouvez utiliser [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) pour obtenir la version de l’image source que vous souhaitez utiliser et l’affecter à une variable. Dans cet exemple, nous obtenons la version d’image `1.0.0`, de la définition `myImageDefinition`, dans la galerie source `myGallery`, dans le groupe de ressources `myResourceGroup`.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Configurez des variables pour les informations de disque.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Créez une configuration de disque, puis le disque, à l’aide de l’ID de version de l’image source. Pour `-GalleryImageReference`, le numéro d’unité logique est nécessaire uniquement si la source est un disque de données.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Créez le disque.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer une version d’image à partir d’un disque géré à l’aide d’[Azure CLI](image-version-managed-image-cli.md) ou de [PowerShell](image-version-managed-image-powershell.md).


