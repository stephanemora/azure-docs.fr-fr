---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486100"
---
Les instantanés incrémentiels sont des sauvegardes des disques managés exécutées à un moment donné et contenant uniquement les modifications apportées depuis la dernière capture instantanée. Lorsque vous essayez de télécharger ou d'utiliser un instantané incrémentiel, tout le disque dur virtuel est utilisé. Cette nouvelle fonctionnalité de capture instantanée des disques managés améliore leur rentabilité, car vous n’êtes pas obligé de stocker l'intégralité du disque avec chaque instantané. Comme pour les instantanés classiques, les instantanés incrémentiels peuvent être utilisés pour créer un disque managé complet ou pour créer un instantané classique.

Il existe un certain nombre de différences entre un instantané incrémentiel et un instantané classique. Les instantanés incrémentiels utilisent toujours le stockage HDD Standard, quel que soit le type de stockage du disque, alors que les instantanés classiques peuvent utiliser des disques SSD premium. Si vous utilisez des instantanés classiques sur le Stockage Premium pour effectuer un scale-up de déploiements de machines virtuelles, nous vous recommandons d'utiliser des images personnalisées sur le stockage standard dans [Shared Image Gallery](../articles/virtual-machines/linux/shared-image-galleries.md). Cela vous aidera à atteindre une plus grande échelle à moindre coût. En outre, les instantanés incrémentiels offrent potentiellement une meilleure fiabilité avec un [stockage redondant interzone](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Si le stockage ZRS est disponible dans la région sélectionnée, l'instantané incrémentiel l'utilisera automatiquement. Si le stockage ZRS n'est pas disponible dans la région, l'instantané utilisera par défaut le [stockage localement redondant](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Vous pouvez remplacer ce comportement et sélectionner un autre type de stockage manuellement, mais nous vous le déconseillons.

Les instantanés incrémentiels offrent également une capacité différentielle, qui est disponible uniquement pour les disques managés. Ils vous permettent d'obtenir les modifications apportées entre deux instantanés incrémentiels des mêmes disques managés, jusqu'au niveau bloc. Vous pouvez utiliser cette fonctionnalité pour réduire l'empreinte de vos données lors de la copie d'instantanés d'une région à une autre.  Par exemple, vous pouvez télécharger le premier instantané incrémentiel en tant qu’objet blob de base dans une autre région. Pour les instantanés incrémentiels suivants, vous pouvez copier uniquement les modifications apportées depuis le dernier instantané sur l’objet blob de base. Après avoir copié les modifications, vous pouvez prendre des instantanés de l’objet blob de base qui représente votre sauvegarde du disque dans une autre région à un moment donné. Vous pouvez restaurer votre disque à partir de l’objet blob de base ou à partir d’un instantané de l’objet blob de base dans une autre région.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagramme illustrant des instantanés incrémentiels copiés dans plusieurs régions. Les instantanés effectuent différents appels d’API jusqu’à ce qu’ils forment éventuellement des objets blob de pages pour chaque instantané.":::