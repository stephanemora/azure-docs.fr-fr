---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145811"
---
## <a name="update-resources"></a>Mettre à jour les ressources

Il existe certaines limitations sur ce qui peut être mis à jour. Les éléments suivants peuvent être mis à jour : 

Galerie d’images partagées :
- Description 

Définition d’image :
- Processeurs virtuels recommandés
- Mémoire recommandée
- Description 
- Date de fin de vie

Version d’image :
- Nombre de réplicas régionaux
- Régions cibles
- Exclusion du dernier
- Date de fin de vie

Si vous prévoyez d’ajouter des régions de réplica, ne supprimez pas l’image managée source. L’image managée source est nécessaire pour la réplication de la version de l’image vers d’autres régions. 

Pour mettre à jour la description de la galerie, utilisez [AzGallery de mise à jour](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Cet exemple montre comment utiliser [AzGalleryImageDefinition de mise à jour](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) pour mettre à jour la date de fin de vie pour notre définition de l’image.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Cet exemple montre comment utiliser [mise à jour-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) à exclure de cette version de l’image d’être utilisé comme le *dernière* image.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous supprimez des ressources, vous devez commencer par le dernier élément dans les ressources imbriquées - la version de l’image. Une fois que les versions sont supprimées, vous pouvez supprimer la définition de l’image. Vous ne pouvez pas supprimer la galerie jusqu'à ce que toutes les ressources sous celui-ci ont été supprimés.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

