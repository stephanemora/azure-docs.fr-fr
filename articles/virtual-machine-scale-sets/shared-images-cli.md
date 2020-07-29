---
title: Utiliser des images partagées de machines virtuelles pour créer un groupe identique dans Azure CLI
description: Découvrez comment utiliser Azure CLI pour créer des images de machine virtuelle partagées dans le but de déployer des groupes de machines virtuelles identiques dans Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: cdcd11b6bd9c773e8018ee303564fff6abd098a6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494902"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Créer et utiliser des images partagées pour les groupe de machines virtuelles identiques avec Azure CLI 2.0

Lorsque vous créez un groupe identique, vous spécifiez une image à utiliser lors du déploiement des instances de machine virtuelle. Une [galerie d’images partagées](shared-image-galleries.md) simplifie considérablement le partage d’images personnalisées dans votre organisation. Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des configurations comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. 

La galerie d’images partagées (Shared Image Gallery) vous permet de partager vos images avec d’autres personnes. Choisissez les images à partager, les régions dans lesquelles vous souhaitez les rendre disponibles et les personnes avec lesquelles vous voulez les partager. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Étapes suivantes

Créer une version d’image à partir d’une [machine virtuelle](../virtual-machines/image-version-vm-cli.md) ou d’une [image managée](../virtual-machines/image-version-managed-image-cli.md).

Pour plus d’informations sur les galeries d’images partagées, consultez la [vue d’ensemble](shared-image-galleries.md). Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).
