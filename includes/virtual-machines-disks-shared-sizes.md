---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471685"
---
Pour l’instant, seuls les disques SSD Premium peuvent activer des disques partagés. Les tailles de disque qui prennent en charge cette fonctionnalité sont P15 et supérieures. Des tailles de disque différentes peuvent avoir une limite `maxShares` différente, que vous ne pouvez pas dépasser lors de la définition de la valeur `maxShares`.

Pour chaque disque, vous pouvez définir une valeur de `maxShares` qui représente le nombre maximal de nœuds pouvant partager simultanément le disque. Par exemple, si vous envisagez de configurer un cluster de basculement à deux nœuds, vous devez définir `maxShares=2`. La valeur maximale est une limite supérieure. Les nœuds peuvent rejoindre ou quitter le cluster (monter ou démonter le disque) tant que le nombre de nœuds est inférieur à la valeur `maxShares` spécifiée.

> [!NOTE]
> La valeur `maxShares` peut être définie ou modifiée uniquement lorsque le disque est détaché de tous les nœuds.

Le tableau suivant illustre les valeurs maximales autorisées pour `maxShares` par taille de disque :

|Tailles du disque  |Limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Les limites d’IOPS et de bande passante pour un disque ne sont pas concernées par la valeur `maxShares`. Par exemple, le nombre maximal d’IOPS d’un disque P15 est de 1100 indépendamment du fait que maxShares = 1 ou maxShares > 1.