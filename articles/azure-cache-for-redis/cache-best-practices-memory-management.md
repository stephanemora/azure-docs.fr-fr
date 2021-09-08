---
title: Meilleures pratiques de gestion de la mémoire
titleSuffix: Azure Cache for Redis
description: Découvrez comment gérer efficacement votre mémoire Azure Cache pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 613d5dd906795b59465458565a00cd37b7374e65
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116257"
---
# <a name="memory-management"></a>Gestion de la mémoire

## <a name="eviction-policy"></a>Stratégie d’éviction

Choisissez une [stratégie d’éviction](https://redis.io/topics/lru-cache) appropriée pour votre application. La stratégie par défaut pour Azure Cache pour Redis est `volatile-lru`, qui spécifie que seules les clés dont la durée de vie a été définie peuvent être supprimées.  Si aucune clé n’a une valeur de durée de vie, le système ne supprime pas de clés.  Si vous souhaitez que le système autorise la suppression de toutes les clés dans des conditions de forte sollicitation de la mémoire, choisissez plutôt la stratégie `allkeys-lru`.

## <a name="keys-expiration"></a>Expiration des clés

Définissez le délai d’expiration de vos clés. L’expiration entraîne la suppression des clés de manière proactive, et non uniquement en cas de forte sollicitation de la mémoire.  Lorsqu’une opération de suppression doit avoir lieu en raison d’une forte sollicitation de la mémoire, une charge supplémentaire sur votre serveur peut en résulter. Pour plus d’informations, consultez la documentation concernant les commandes [EXPIRE](https://redis.io/commands/expire) et [EXPIREAT](https://redis.io/commands/expireat).

## <a name="minimize-memory-fragmentation"></a>Réduire la fragmentation de la mémoire

Les valeurs élevées peuvent faire en sorte que la mémoire soit fragmentée lors de la suppression et peuvent entraîner une utilisation élevée de la mémoire et une charge du serveur.

## <a name="monitor-memory-usage"></a>Surveiller l’utilisation de la mémoire

Ajoutez une surveillance de l’utilisation de la mémoire pour vous assurer de ne pas être à court de mémoire et de pouvoir mettre à l’échelle votre cache avant de rencontrer des problèmes.

## <a name="configure-your-maxmemory-reserved-setting"></a>Configurer le paramètre maxmemory-reserved

Configurez le [paramètre maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) afin d’améliorer la réactivité du système :

* Une valeur suffisante de réservation est particulièrement importante pour les charges de travail nécessitant beaucoup d’écritures, ou si vous stockez des volumes de données de 100 Ko ou plus dans votre cache. Commencez par 10 % de la taille de votre cache, puis augmentez ce pourcentage si vous avez des charges lourdes en écritures.

* Le paramètre  `maxmemory-reserved`  détermine la quantité de mémoire (en mégaoctets par instance dans un cluster) réservée à des opérations non cache telles que la réplication pendant le basculement. La définition de ce paramètre vous permet d’avoir une expérience de serveur Redis plus cohérente lorsque votre charge varie. Cette valeur doit être plus élevée pour les charges de travail qui écrivent de grandes quantités de données. Lorsque la mémoire est réservée pour ces opérations, elle n’est pas disponible pour le stockage des données mises en cache.

* Le paramètre  `maxfragmentationmemory-reserved`  détermine la quantité de mémoire (en mégaoctets par instance dans un cluster) réservée pour la fragmentation de la mémoire. La définition de ce paramètre vous permet d’avoir un serveur Redis plus cohérent lorsque le cache est plein ou presque, et que le taux de fragmentation est élevé. Lorsque la mémoire est réservée pour ces opérations, elle n’est pas disponible pour le stockage des données mises en cache.

* Un aspect à prendre en compte lors du choix d’une nouvelle valeur de réservation de mémoire (`maxmemory-reserved`  ou  `maxfragmentationmemory-reserved`) est la manière dont cette modification peut affecter un cache déjà en cours d’exécution, qui contient de grandes quantités de données. Par exemple, si vous disposez d’un cache de 53 Go avec 49 Go de données, que vous modifiez la valeur de réservation en la définissant sur 8 Go, la mémoire maximale disponible pour le système chute à 45 Go. Si vos valeurs  `used_memory`  ou  `used_memory_rss`  actuelles sont supérieures à la nouvelle limite de 45 Go, le système doit supprimer des données jusqu’à ce que les valeurs  `used_memory`  et  `used_memory_rss`  soient toutes deux inférieures à 45 Go. La suppression de données peut augmenter la fragmentation de la charge et de la mémoire du serveur. Pour plus d’informations sur les métriques de cache, telles que  `used_memory`  et  `used_memory_rss`, consultez  [Métriques et intervalles de création de rapports disponibles](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).
