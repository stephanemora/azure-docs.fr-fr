---
title: fichier descriptif
description: fichier descriptif
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/03/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 78721aa407977262747c43be90001da2343322aa
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123483870"
---
Pour le moment, seuls les disques Ultra, les disques SSD Premium et les disques SSD standard peuvent activer des disques partagés. Des tailles de disque différentes peuvent avoir une limite `maxShares` différente, que vous ne pouvez pas dépasser lors de la définition de la valeur `maxShares`. Pour les disques SSD Premium, les tailles de disque qui prennent en charge le partage de leurs disques sont P15 et supérieures.

Pour chaque disque, vous pouvez définir une valeur de `maxShares` qui représente le nombre maximal de nœuds pouvant partager simultanément le disque. Par exemple, si vous envisagez de configurer un cluster de basculement à deux nœuds, vous devez définir `maxShares=2`. La valeur maximale est une limite supérieure. Les nœuds peuvent rejoindre ou quitter le cluster (monter ou démonter le disque) tant que le nombre de nœuds est inférieur à la valeur `maxShares` spécifiée.

> [!NOTE]
> La valeur `maxShares` peut être définie ou modifiée uniquement lorsque le disque est détaché de tous les nœuds.

### <a name="premium-ssd-ranges"></a>Plages des disques SSD Premium

Le tableau suivant illustre les valeurs maximales autorisées pour `maxShares` par taille de SSD Premium :

|Tailles du disque  |Limite maxShares  |
|---------|---------|
|P1, P2, P3, P4, P6, P10, P15, P20     |3         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Les limites d’IOPS et de bande passante pour un disque ne sont pas concernées par la valeur `maxShares`. Par exemple, le nombre maximal d’IOPS d’un disque P15 est de 1100 indépendamment du fait que maxShares = 1 ou maxShares > 1.

### <a name="standard-ssd-ranges"></a>Plages de SSD Standard

Le tableau suivant illustre les valeurs maximales autorisées pour `maxShares` par taille de SSD Standard :

|Tailles du disque  |Limite maxShares  |
|---------|---------|
|E1, E2, E3, E4, E6, E10, E15, E20     |3         |
|E30, E40, E50     |5         |
|E60, E70, E80     |10         |

Les limites d’IOPS et de bande passante pour un disque ne sont pas concernées par la valeur `maxShares`. Par exemple, le nombre maximal d’IOPS d’un disque E15 est de 500 indépendamment du fait que maxShares = 1 ou maxShares > 1.

### <a name="ultra-disk-ranges"></a>Plages des disques Ultra

La valeur `maxShares` minimale est 1, alors que la valeur `maxShares` maximale est 5. Il n’y a aucune restriction de taille sur les disques Ultra ; un disque Ultra, quelle que soit sa taille, peut utiliser n’importe quelle valeur pour `maxShares`, jusqu’à la valeur maximale incluse.