---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ce964ac197fbff64bbb7cc36e8c2bf762f93663f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84337346"
---
En préversion, l’activation de disques partagés est disponible uniquement pour une partie des types de disques. Seuls les disques Ultra et les disques SSD Premium peuvent activer des disques partagés. Chaque disque managé pour lequel la fonctionnalité Disques partagés est activée est soumis aux limitations suivantes, selon le type disque :

### <a name="ultra-disks"></a>Disques Ultra

Les disques Ultra ont leur propre liste de limitations sans lien avec les disques partagés. Pour connaître les limitations des disques Ultra, reportez-vous à [Utilisation de disques Ultra Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Quand vous partagez des disques Ultra, ils présentent les limitations supplémentaires suivantes :

- Limités à la prise en charge d’Azure Resource Manager ou des kits SDK.
- Seuls les disques de base peuvent être utilisés avec certaines versions de la fonctionnalité Cluster de basculement de Windows Server. Pour plus d’informations, consultez [Configuration matérielle requise pour le clustering de basculement et options de stockage](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>SSD Premium

- Actuellement pris en charge dans la région USA Centre-Ouest uniquement.
- Toutes les machines virtuelles partageant un disque doivent être déployées dans les mêmes [groupes de placement de proximité](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Activation possible uniquement sur des disques de données, et non sur des disques de système d’exploitation.
- Seuls les disques de base peuvent être utilisés avec certaines versions de la fonctionnalité Cluster de basculement de Windows Server. Pour plus d’informations, consultez [Configuration matérielle requise pour le clustering de basculement et options de stockage](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- La mise en cache de l’hôte ReadOnly n’est pas disponible pour les disques SSD Premium avec `maxShares>1`.
- Les groupes à haute disponibilité et les groupes de machines virtuelles identiques peuvent uniquement être utilisés avec `FaultDomainCount` défini sur 1.
- La prise en charge de Sauvegarde Azure et d’Azure Site Recovery n’est pas encore disponible.

Si vous souhaitez essayer la fonctionnalité Disques partagés, [inscrivez-vous à la préversion](https://aka.ms/AzureSharedDiskPreviewSignUp).
