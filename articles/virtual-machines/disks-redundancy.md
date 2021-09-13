---
title: Options de redondance pour les disques managés Azure
description: Apprenez-en davantage sur le stockage redondant interzone et le stockage localement redondant pour les disques managés Azure.
author: roygara
ms.author: rogarana
ms.date: 07/12/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: b24f24547e45b8ef580715828839ec1b1e5b4618
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692691"
---
# <a name="redundancy-options-for-managed-disks"></a>Options de redondance pour les disques managés

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les disques managés Azure offrent deux options de redondance du stockage : le stockage redondant interzone (ZRS), proposé en préversion, et le stockage localement redondant. Le stockage ZRS offre une disponibilité plus élevée pour les disques managés que le stockage localement redondant (LRS). Toutefois, la latence d’écriture des disques LRS est meilleure que celle des disques ZRS. En effet, les disques LRS écrivent les données sur trois copies de manière synchrone dans un centre de données unique.

## <a name="locally-redundant-storage-for-managed-disks"></a>Stockage localement redondant pour les disques managés

Le stockage localement redondant (LRS) réplique vos données trois fois au sein d’un même centre de données dans la région sélectionnée. Il protège vos données contre les défaillances de disque et de rack du serveur. Pour protéger un disque LRS d’une défaillance zonale, par exemple une catastrophe naturelle ou tout autre sinistre, effectuez les étapes suivantes :

- Utilisez des applications qui peuvent écrire des données de manière synchrone dans deux zones, puis effectuez un basculement automatique vers l’une des deux zones en cas de sinistre.
    - SQL Server AlwaysOn en est un exemple.
- Effectuez des sauvegardes fréquentes des disques LRS avec des instantanés ZRS.
- Activez la reprise d’activité interzone pour les disques LRS avec [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Toutefois, la reprise d’activité interzone n’offre pas d’objectif de point de récupération (RPO) zéro.

Si votre workflow ne prend pas en charge les écritures synchrones au niveau de l’application entre les zones, ou si votre application doit respecter un RPO zéro, les disques ZRS représentent la solution idéale.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Stockage redondant interzone pour les disques managés (préversion)

Le stockage ZRS (stockage redondant interzone) réplique de manière synchrone votre disque managé Azure sur trois zones de disponibilité Azure dans la région de votre choix. Chaque zone de disponibilité est un emplacement physique distinct avec une alimentation, un refroidissement et une mise en réseau indépendants. 

Un disque ZRS (préversion) vous permet d’effectuer une reprise d’activité après sinistre dans les zones de disponibilité. En cas de défaillance d’une zone, un disque ZRS peut être attaché à une machine virtuelle située dans une autre zone. Les disques ZRS peuvent également être partagés entre plusieurs machines virtuelles pour une meilleure disponibilité avec des applications en cluster ou des applications distribuées telles que SQL FCI, SAP ASCS/SCS ou GFS2. Un disque ZRS partagé peut être attaché à des machines virtuelles principale et secondaire dans différentes zones pour tirer parti de ZRS et des [zones de disponibilité](../availability-zones/az-overview.md). En cas de défaillance de la zone principale, vous pouvez rapidement basculer vers la machine virtuelle secondaire à l’aide de la [réservation persistante SCSI](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="billing-implications"></a>Implications de facturation

Pour plus d’informations, consultez la [page des tarifs Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Comparaison avec d’autres types de disques

À l’exception d’une latence d’écriture plus importante, les disques utilisant ZRS sont identiques aux disques utilisant LRS. Ils ont les mêmes cibles de mise à l’échelle. [Évaluez vos disques](disks-benchmarks.md) pour simuler la charge de travail de votre application et comparer la latence entre les disques LRS et ZRS. 

### <a name="limitations"></a>Limites

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment créer un disque ZRS, consultez [Déployer un disque managé ZRS](disks-deploy-zrs.md).
