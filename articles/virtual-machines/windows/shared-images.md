---
title: Créer des images de machine virtuelle partagées avec Azure PowerShell | Microsoft Docs
description: Apprenez à utiliser Azure PowerShell pour créer une image de machine virtuelle partagée dans Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 0a44f7d9c18e406850e2dbfb091088be0b8c2113
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235897"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Créer une galerie d’images partagées avec Azure PowerShell 

Une [galerie d’images partagées](shared-image-galleries.md) simplifie considérablement le partage d’images personnalisées dans votre organisation. Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des tâches de déploiement comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. 

La galerie d’images partagées vous permet de partager vos images de machine virtuelle personnalisées avec d’autres personnes de votre organisation, dans ou entre les régions, à l’intérieur d’un locataire AAD. Choisissez les images à partager, les régions dans lesquelles vous souhaitez les rendre disponibles et les personnes avec lesquelles vous voulez les partager. Vous pouvez créer plusieurs galeries afin de regrouper logiquement les images partagées. 

La galerie est une ressource de niveau supérieur qui fournit le contrôle d’accès en fonction du rôle (RBAC) complet. Les versions des images peuvent être gérées, et vous pouvez choisir de répliquer chaque version d’image vers un autre ensemble de régions Azure. La galerie fonctionne uniquement avec les images managées.

La fonctionnalité Galerie d’images partagées présente plusieurs types de ressources. Dans cet article, nous allons utiliser ou générer ce qui suit :

| Ressource | Description |
|----------|------------|
| **Image managée** | Il s’agit d’une image de base pouvant être utilisée seule ou pour créer une **version de l’image**  dans une galerie d’images. Les images managées sont créées à partir de machines virtuelles généralisées. Une image managée est un type spécial de disque dur virtuel qui peut être utilisé pour définir plusieurs machines virtuelles et qui peut maintenant être utilisé pour créer des versions d’image partagée. |
| **Galerie d’images** | Tout comme la Place de marché Azure, une **galerie d’images** est un dépôt permettant de gérer et partager des images, mais vous contrôlez les utilisateurs qui y ont accès. |
| **Définition d'image** | Les images sont définies dans une galerie et incluent des informations sur l’image et sur les exigences relatives à son utilisation en interne. Ces informations indiquent, par exemple, si l’image est Windows ou Linux, et comprennent les notes de publication et les exigences de mémoire maximale et minimale. Il s’agit d’une définition de type d’image. |
| **Version de l’image** | Une **version d’image** est ce qui vous permet de créer une machine virtuelle quand vous utilisez une galerie. Vous pouvez avoir plusieurs versions d’une image en fonction des besoins de votre environnement. Tout comme une image managée, quand vous utilisez une **version d’image** pour créer une machine virtuelle, la version d’image permet de créer des disques pour la machine virtuelle. Les versions d’image peuvent être utilisées plusieurs fois. |

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre l’exemple de cet article, vous devez avoir l’image managée. Vous pouvez suivre le [Tutoriel : créer une image personnalisée d’une machine virtuelle Azure avec Azure PowerShell](tutorial-custom-images.md) si nécessaire. Si l’image managée contient un disque de données, la taille de disque de données ne peut pas être supérieur à 1 To.

Au cours de cet article, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]
 
## <a name="create-vms-from-an-image"></a>Créer des machines virtuelles à partir d’une image

Une fois que la version de l’image est complète, vous pouvez créer une ou plusieurs nouvelles machines virtuelles. À l’aide du paramètre simplifié défini pour la cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), vous devez simplement fournir l’ID de l’image de la version de l’image. 

Cet exemple crée une machine virtuelle nommée *myVMfromImage*, dans le groupe *myResourceGroup* dans le centre de données *USA Est*.

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $imageVersion.Id `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Étapes suivantes
[Générateur d’images Azure (aperçu)](image-builder-overview.md) peut aider à automatiser la création d’images version, vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une version existante de l’image](image-builder-gallery-update-image-version.md). 

Vous pouvez également créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Créer une machine virtuelle à partir d’une version d’image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Pour plus d’informations sur les galeries d’images partagées, consultez la [vue d’ensemble](shared-image-galleries.md). Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).

