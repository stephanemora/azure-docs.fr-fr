---
title: Répertorier, mettre à jour et supprimer des ressources d’image à l’aide de PowerShell
description: Répertoriez, mettez à jour et supprimez des ressources d’image dans votre galerie d’images partagées à l’aide d’Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/27/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 91664719f8af1cd44336a7701cd561eeea6d8279
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698413"
---
# <a name="list-update-and-delete-image-resources-using-powershell"></a>Répertorier, mettre à jour et supprimer des ressources d’image à l’aide de PowerShell 

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Vous pouvez gérer vos ressources de galerie d’images partagées à l’aide d’Azure PowerShell.

[!INCLUDE [virtual-machines-common-gallery-list-ps.md](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Étapes suivantes

[Générateur d’image Azure (préversion)](./image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./linux/image-builder-gallery-update-image-version.md).