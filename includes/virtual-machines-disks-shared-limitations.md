---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471684"
---
Dans la préversion, les disques managés pour lesquels la fonctionnalité Disques partagés est activée sont soumis aux limitations suivantes :

- Actuellement disponibles uniquement avec les disques SSD Premium.
- Actuellement pris en charge dans la région USA Centre-Ouest uniquement.
- Toutes les machines virtuelles partageant un disque doivent être déployées dans les mêmes [groupes de placement de proximité](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Activation possible uniquement sur des disques de données, et non sur des disques de système d’exploitation.
- Seuls les disques de base peuvent être utilisés avec certaines versions de la fonctionnalité Cluster de basculement de Windows Server. Pour plus d’informations, consultez [Configuration matérielle requise pour le clustering de basculement et options de stockage](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- La mise en cache de l’hôte ReadOnly n’est pas disponible pour les disques SSD Premium avec `maxShares>1`.
- Les groupes à haute disponibilité et les groupes de machines virtuelles identiques peuvent uniquement être utilisés avec `FaultDomainCount` défini sur 1.
- La prise en charge de Sauvegarde Azure et d’Azure Site Recovery n’est pas encore disponible.

Si vous souhaitez essayer la fonctionnalité Disques partagés, [inscrivez-vous à la préversion](https://aka.ms/AzureSharedDiskPreviewSignUp).
