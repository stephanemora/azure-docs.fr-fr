---
title: Créer des galeries d’images partagées avec l’interface de ligne de commande Azure
description: Dans cet article, vous allez apprendre à utiliser l’interface de ligne de commande Azure pour créer une image partagée d’une machine virtuelle dans Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 991b5363b180651b775bd46a0a1353fd124d34e6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557726"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Créer une galerie d’images partagées avec Azure CLI

Une [galerie d’images partagées](./shared-image-galleries.md) simplifie considérablement le partage d’images personnalisées dans votre organisation. Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des configurations comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. 

Shared Image Gallery vous permet de partager vos images de machines virtuelles personnalisées avec d’autres utilisateurs. Choisissez les images à partager, les régions dans lesquelles vous souhaitez les rendre disponibles et les personnes avec lesquelles vous voulez les partager. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Étapes suivantes

Créez une version de l’image à partir d’une [machine virtuelle](image-version-vm-cli.md) ou d’une [image managée](image-version-managed-image-cli.md) à l’aide d’Azure CLI.

Pour plus d’informations sur les galeries d’images partagées, consultez la [vue d’ensemble](./shared-image-galleries.md). Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).