---
title: Utiliser des images de machine virtuelle partagées pour créer un groupe identique dans Azure | Microsoft Docs
description: Découvrez comment utiliser Azure PowerShell pour créer des images de machine virtuelle partagées dans le but de déployer des groupes de machines virtuelles identiques dans Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 7e67e7836b1d80e623a11e552c81750bc6133205
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981636"
---
# <a name="preview-create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Aperçu : Créer et utiliser des images partagées pour les groupe de machines virtuelles identiques avec Azure PowerShell

Lorsque vous créez un groupe identique, vous spécifiez une image à utiliser lors du déploiement des instances de machine virtuelle. Le service Galerie d’images partagées simplifie considérablement le partage d’images personnalisées au sein de votre organisation. Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des configurations comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. 

La galerie d’images partagées vous permet de partager vos images de machine virtuelle personnalisées avec d’autres personnes de votre organisation, dans ou entre les régions, à l’intérieur d’un locataire AAD. Choisissez les images à partager, les régions dans lesquelles vous souhaitez les rendre disponibles et les personnes avec lesquelles vous voulez les partager. Vous pouvez créer plusieurs galeries afin de regrouper logiquement les images partagées. 

La galerie est une ressource de niveau supérieur qui fournit le contrôle d’accès en fonction du rôle (RBAC) complet. Les versions des images peuvent être gérées, et vous pouvez choisir de répliquer chaque version d’image vers un autre ensemble de régions Azure. La galerie fonctionne uniquement avec les images managées. 

La fonctionnalité Galerie d’images partagées présente plusieurs types de ressources. Dans cet article, nous allons utiliser ou générer ce qui suit :

| Ressource | Description|
|----------|------------|
| **Image managée** | Il s’agit d’une image de base pouvant être utilisée seule ou pour créer une **version de l’image**  dans une galerie d’images. Les images managées sont créées à partir de machines virtuelles généralisées. Une image managée est un type spécial de disque dur virtuel qui peut être utilisé pour définir plusieurs machines virtuelles et qui peut maintenant être utilisé pour créer des versions d’image partagée. |
| **Galerie d’images** | Tout comme la Place de marché Azure, une **galerie d’images** est un dépôt permettant de gérer et partager des images, mais vous contrôlez les utilisateurs qui y ont accès. |
| **Définition d'image** | Les images sont définies dans une galerie et incluent des informations sur l’image et sur les exigences relatives à son utilisation en interne. Ces informations indiquent, par exemple, si l’image est Windows ou Linux, et comprennent les notes de publication et les exigences de mémoire maximale et minimale. Il s’agit d’une définition de type d’image. |
| **Version de l’image** | Une **version d’image** est ce qui vous permet de créer une machine virtuelle quand vous utilisez une galerie. Vous pouvez avoir plusieurs versions d’une image en fonction des besoins de votre environnement. Tout comme une image managée, quand vous utilisez une **version d’image** pour créer une machine virtuelle, la version d’image permet de créer des disques pour la machine virtuelle. Les versions d’image peuvent être utilisées plusieurs fois. |

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

## <a name="before-you-begin"></a>Avant de commencer

Les étapes ci-dessous expliquent comment prendre une machine virtuelle existante et la transformer en une image personnalisée réutilisable que vous pouvez utiliser pour créer de nouvelles instances de machines virtuelles.

Pour suivre l’exemple de cet article, vous devez avoir l’image managée. Vous pouvez suivre le [Tutoriel : Créer et utiliser une image personnalisée pour des groupes de machines virtuelles identiques avec Azure PowerShell](tutorial-use-custom-image-powershell.md) si nécessaire. Au cours de cet article, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Créer un groupe identique à partir de la version d’image personnalisée

Créez un groupe de machines virtuelles identiques avec [New-AzVmss](/powershell/module/az.compute/new-azvmss). L'exemple suivant crée un groupe identique à partir de la nouvelle version d’image dans le centre de données USA Ouest. Les ressources réseau Azure pour le réseau virtuel, l’adresse IP publique et l’équilibreur de charge sont automatiquement créées. Quand vous y êtes invité, définissez vos propres informations d’identification d’administration pour les instances de machine virtuelle du groupe identique :

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName myVMSSRG `
  -Location 'South Central US' `
  -VMScaleSetName 'myScaleSet' `
  -VirtualNetworkName 'myVnet' `
  -SubnetName 'mySubnet'`
  -PublicIpAddressName 'myPublicIPAddress' `
  -LoadBalancerName 'myLoadBalancer' `
  -UpgradePolicyMode 'Automatic' `
  -ImageName $imageVersion.Id
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myGalleryRG
Remove-AzResourceGroup -Name myVMSSRG
```


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Créer une machine virtuelle à partir d’une version d’image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Pour plus d’informations sur les galeries d’images partagées, consultez la [vue d’ensemble](shared-image-galleries.md). Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).
