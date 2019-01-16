---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3ec5b9c6357f0d075ddd9b0fd5c8a88ee2846209
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192725"
---
## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.


## <a name="preview-register-the-feature"></a>Aperçu : Inscrire la fonctionnalité

La galerie d’images partagées est en préversion, mais vous devez l’inscrire pour pouvoir l’utiliser. Pour inscrire la fonctionnalité de galerie d’images partagées :

```azurepowershell-interactive
Register-AzureRmProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>Obtenir l'image managée

Pour afficher une liste d'images disponibles dans un groupe de ressources, utilisez [Get-AzureRmImage](/powershell/module/AzureRM.Compute/get-azurermimage). Une fois le nom de l'image et son groupe de ressources connus, vous pouvez réutiliser `Get-AzureRmImage` pour obtenir l’objet image et le stocker dans une variable à des fins d'utilisation ultérieure. Cet exemple permet d'obtenir une image nommée *myImage* à partir du groupe de ressources « myResourceGroup » et l’assigne à la variable *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzureRmImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Créer une galerie d’images 

Une galerie d’images est la ressource principale utilisée pour activer le partage d’image. Les noms de galerie doivent être uniques dans votre abonnement. Créez une galerie d’images à l’aide de [New-AzureRmGallery](/powershell/module/AzureRM.Compute/new-azurermgallery). L’exemple suivant crée une galerie nommée *myGallery* dans le groupe de ressources *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzureRMResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzureRmGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Créer une définition d’image 

Créez la définition d'image de galerie à l'aide de [New-AzureRmGalleryImageDefinition](/powershell/module/azurerm.compute/new-azurermgalleryimageversion). Dans cet exemple, l’image de galerie est nommée *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzureRmGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

Dans une prochaine version, vous serez en mesure d’utiliser les valeurs **-Publisher**, **-Offer** et **-Sku** que vous avez personnellement définies pour rechercher et spécifier une définition d'image, puis de créer une machine virtuelle à l'aide de la dernière version d'image issue de la définition d'image correspondante. Par exemple, voici trois définitions d'image et leurs valeurs :

|Définition de l’image|Publisher|Offre|Sku|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Test|standardOffer|testSku|

Ces trois définitions présentent des ensembles de valeurs uniques. Dans une prochaine version, vous serez en mesure de combiner ces valeurs pour demander la dernière version d’une image spécifique. 

```powershell
# The following should set the source image as myImage1 from the table above
$vmConfig = Set-AzureRmVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Ce processus est semblable à celui que vous utilisez pour spécifier ces valeurs pour les [images de Place de marché Azure](../articles/virtual-machines/windows/cli-ps-findimage.md) et créer une machine virtuelle. Dans cet esprit, chaque définition d'image doit disposer d'un ensemble unique de ces valeurs. Des versions d'image peuvent partager une ou deux de ces valeurs, mais pas les trois. 

##<a name="create-an-image-version"></a>Créer une version d’image

Créez une version d'image à partir d’une image managée à l’aide de [New-AzureRmGalleryImageVersion](/powershell/module/AzureRM.Compute/new-azurermgalleryimageversion). Dans cet exemple, la version d'image, *1.0.0*, elle est répliquée dans les deux centres de données *USA Centre-Ouest* et *USA Centre Sud*.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

La réplication de l'image dans toutes les régions cibles peut nécessiter un certain temps et nous avons donc créé une tâche pour en suivre la progression. Pour afficher la progression de la tâche, entrez `$job.State`.

```azurepowershell-interactive
$job.State
```

