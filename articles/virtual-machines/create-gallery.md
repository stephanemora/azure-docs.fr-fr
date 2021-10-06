---
title: Créer une galerie Shared Image Gallery
description: Découvrez comment créer une Galerie d’images partagées dans Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/15/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: c7462886c2f85e07f060caa7a11a39a782e38cea
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452621"
---
# <a name="create-a-gallery-for-storing-and-sharing-images"></a>Créez une galerie pour le stockage et le partage d’images


Une [galerie d’images partagées](./shared-image-galleries.md) simplifie considérablement le partage d’images personnalisées dans votre organisation. Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des tâches de déploiement comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. 

La galerie d’images partagées vous permet de partager vos images de machine virtuelle personnalisées avec d’autres personnes de votre organisation, dans ou entre les régions, à l’intérieur d’un locataire AAD. Choisissez les images à partager, les régions dans lesquelles vous souhaitez les rendre disponibles et les personnes avec lesquelles vous voulez les partager. Vous pouvez créer plusieurs galeries afin de regrouper logiquement les images partagées. 

La galerie est une ressource de niveau supérieur qui fournit le contrôle d'accès en fonction du rôle Azure (Azure RBAC) complet. Les versions des images peuvent être gérées, et vous pouvez choisir de répliquer chaque version d’image vers un autre ensemble de régions Azure. La galerie fonctionne uniquement avec les images managées.

La fonctionnalité Galerie d’images partagées présente plusieurs types de ressources. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="create-a-gallery"></a>Créer une galerie

Une galerie d’images est la ressource principale utilisée pour activer le partage d’image. Les caractères autorisés pour le nom de galerie sont les lettres majuscules ou minuscules, les chiffres et les points. Le nom de galerie ne peut pas contenir de tirets. Les noms de galerie doivent être uniques dans votre abonnement. 

Choisissez une option ci-dessous pour créer votre galerie :

### <a name="portal"></a>[Portail](#tab/portal)

L’exemple suivant crée une galerie nommée *myGallery* dans le groupe de ressources *myGalleryRG*.

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Utilisez le type **Galerie d’images partagées** dans la zone de recherche, puis sélectionnez **Galerie d’images partagées** dans les résultats.
1. Sur la page **Galerie d’images partagées**, cliquez sur **Ajouter**.
1. Dans la page **Créer une galerie Shared Image Gallery**, sélectionnez l’abonnement approprié.
1. Dans **Groupe de ressources**, sélectionnez **Créer** et saisissez *myResourceGroup* comme nom.
1. Dans **Nom**, saisissez *myGallery* comme nom de la galerie.
1. Laissez la valeur par défaut pour **Région**.
1. Vous pouvez saisir une brève description de la galerie, par exemple *Ma galerie d’images pour le test.* Cliquez ensuite sur **Vérifier + créer**.
1. Une fois la validation réussie, sélectionnez **Créer**.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Créez une galerie d’images à l’aide de [az sig create](/cli/azure/sig#az_sig_create). L’exemple suivant crée un groupe de ressources nommé *myGalleryRG* dans *USA Est* et une galerie nommée *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Créez une galerie d’images à l’aide de [New-AzureRmGallery](/powershell/module/az.compute/new-azgallery). L’exemple suivant crée une galerie nommée *myGallery* dans le groupe de ressources *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


### <a name="rest"></a>[REST](#tab/rest)

Utilisez l'[API REST](/rest/api/resources/resource-groups/create-or-update) pour créer un groupe de ressources.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

Utilisez l'[API REST](/rest/api/compute/galleries/create-or-update) pour créer une galerie.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Shared Image Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>Étapes suivantes

Créez une [définition d’image et une version d’image](image-version.md).

[Générateur d’image Azure (préversion)](./image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./windows/image-builder-gallery-update-image-version.md). 

Vous pouvez également créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-image-version-create/)