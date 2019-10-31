---
title: Résoudre les problèmes liés à Azure Cache pour Redis | Microsoft Docs
description: Découvrir comment résoudre les problèmes de perte de données avec Azure Cache for Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 507a8aef36e4ea5db0b4fdecb54876158398d96a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795245"
---
# <a name="troubleshoot-data-loss"></a>Résoudre les problèmes de perte de données

Cette section explique comment diagnostiquer les pertes de données réelles ou perçues qui peuvent se produire dans Azure Cache pour Redis.

- [Perte partielle des clés](#partial-loss-of-keys)
- [Perte majeure ou totale des clés](#major-or-complete-loss-of-keys)

> [!NOTE]
> Dans ce guide, plusieurs procédures de résolution de problèmes comprennent des instructions pour exécuter des commandes Redis et surveiller diverses mesures de performances. Pour plus d’informations et d’instructions, consultez les articles dans la section [Informations supplémentaires](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perte partielle des clés

Redis ne supprime pas de façon aléatoire les clés une fois celles-ci stockées en mémoire. Toutefois, il les supprime en réponse à des stratégies d’expiration ou d’éviction et à la suite de commandes de suppression de clé explicites. Par ailleurs, les clés qui ont été écrites sur le nœud maître dans un cache Azure Cache pour Redis Premium ou Standard peuvent ne pas être immédiatement disponibles sur un réplica. Les données sont répliquées du maître vers le réplica de manière asynchrone et non bloquante.

Si vous constatez que des clés ont disparu de votre cache, passez en revue les points suivants pour tenter de déterminer la cause du problème :

| Cause : | Description |
|---|---|
| [Expiration des clés](#key-expiration) | Les clés sont supprimées en raison de délais d’expiration définis |
| [Éviction des clés](#key-eviction) | Les clés sont supprimées pour cause de sollicitation de la mémoire |
| [Suppression des clés](#key-deletion) | Les clés sont supprimées par des commandes de suppression explicites |
| [Réplication asynchrone](#async-replication) | Les clés ne sont pas disponibles sur un réplica en raison de délais affectant la réplication des données |

### <a name="key-expiration"></a>Expiration des clés

Redis supprime automatiquement une clé si un délai d’expiration lui est affecté et que ce délai est dépassé. Pour plus d’informations sur l’expiration des clés Redis, consultez la documentation de la commande [EXPIRE](http://redis.io/commands/expire). Vous pouvez également définir des valeurs de délai d’expiration à l’aide des commandes [SET](http://redis.io/commands/set), [SETEX](https://redis.io/commands/setex) et [GETSET](https://redis.io/commands/getset) et d’autres commandes \*STORE.

Vous pouvez utiliser la commande [INFO](http://redis.io/commands/info) pour obtenir des statistiques sur le nombre de clés ayant expiré. La section *Stats* montre le nombre total de clés ayant expiré. La section *Keyspace* fournit des informations supplémentaires sur le nombre de clés avec des délais d’expiration et la valeur de délai d’expiration moyenne.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Vous pouvez également examiner les métriques de diagnostic de votre cache pour voir s’il existe une corrélation entre le moment où la clé a disparu et un pic d’expiration des clés. Pour plus d’informations sur l’utilisation des notifications Keyspace ou de MONITOR pour déboguer ces types de problèmes, consultez l’[annexe](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix).

### <a name="key-eviction"></a>Éviction des clés

Redis a besoin d’espace mémoire pour stocker les données. Il supprime donc des clés pour libérer de la mémoire si nécessaire. Quand les valeurs **used_memory** ou **used_memory_rss** dans la commande [INFO](http://redis.io/commands/info) s’approchent du paramètre **maxmemory** configuré, Redis commence à supprimer des clés de la mémoire en fonction de la [stratégie de cache](http://redis.io/topics/lru-cache).

Utilisez la commande [INFO](http://redis.io/commands/info) pour surveiller le nombre de clés supprimées.

```
# Stats

evicted_keys:13224
```

Vous pouvez également examiner les métriques de diagnostic de votre cache pour voir s’il existe une corrélation entre le moment où la clé a disparu et un pic d’éviction des clés. Pour plus d’informations sur l’utilisation des notifications Keyspace ou de MONITOR pour déboguer ces types de problèmes, consultez l’[annexe](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix).

### <a name="key-deletion"></a>Suppression des clés

Les clients Redis peuvent émettre la commande [DEL](http://redis.io/commands/del) ou [HDEL](http://redis.io/commands/hdel) pour supprimer explicitement des clés de Redis. Utilisez la commande [INFO](http://redis.io/commands/info) pour suivre le nombre d’opérations de suppression. Si des commandes DEL ou HDEL ont été appelées, elles sont listées dans la section *Commandstats*.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Réplication asynchrone

Tout cache Azure Cache pour Redis de niveau Standard ou Premium est configuré avec un nœud maître et au moins un réplica. Un processus en arrière-plan copie les données de façon asynchrone du maître vers un réplica. Le site web [redis.io](http://redis.io/topics/replication) décrit le fonctionnement général de la réplication des données dans Redis. Dans les scénarios où les clients écrivent fréquemment dans Redis, vous pouvez subir une perte partielle de données étant donné que cette réplication est garantie instantanée. Par exemple, si le maître tombe en panne _après_ qu’un client y écrit une clé mais _avant_ que le processus en arrière-plan n’envoie cette clé au réplica, cette clé est perdue quand le réplica prend le relais en tant que nouveau maître.

## <a name="major-or-complete-loss-of-keys"></a>Perte majeure ou totale des clés

Si vous constatez que la plupart ou la totalité des clés ont disparu de votre cache, examinez les points suivants pour tenter de déterminer la cause du problème :

| Cause : | Description |
|---|---|
| [Vidage des clés](#key-flushing) | Les clés ont été vidées manuellement |
| [Sélection incorrecte de la base de données](#incorrect-database-selection) | Redis est configuré pour utiliser une base de données autre que celle par défaut |
| [Échec de l’instance de Redis](#redis-instance-failure) | Les clés sont supprimées par des commandes de suppression explicites |

### <a name="key-flushing"></a>Vidage des clés

Les clients peuvent appeler la commande [FLUSHDB](http://redis.io/commands/flushdb) pour supprimer toutes les clés d’une base de données **unique** ou [FLUSHALL](http://redis.io/commands/flushall) pour supprimer toutes les clés de **toutes** les bases de données dans un cache Redis. Utilisez la commande [INFO](http://redis.io/commands/info) pour déterminer si les clés ont été vidées. Elle indique si la commande FLUSH a été appelée dans la section *Commandstats*.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Sélection incorrecte de la base de données

Azure Cache pour Redis utilise la base de données **db0** par défaut. Si vous basculez vers une autre base de données (comme db1) et que vous essayez d’y lire des clés, Redis ne les trouve pas dans la mesure où chaque base de données est une unité séparée de manière logique qui contient un jeu de données différent. Utilisez la commande [SELECT](http://redis.io/commands/select) pour utiliser d’autres bases de données disponibles et rechercher des clés dans chacune d’elles.

### <a name="redis-instance-failure"></a>Échec de l’instance de Redis

Redis est un magasin de données en mémoire. Les données sont conservées sur les machines physiques ou virtuelles qui hébergent Redis. Une instance d’Azure Cache pour Redis de niveau De base ne s’exécute que sur une seule machine virtuelle. Si cette machine virtuelle tombe en panne, toutes les données que vous avez stockées dans le cache sont perdues. Les caches de niveau Standard ou Premium utilisent deux machines virtuelles dans une configuration répliquée, offrant ainsi plus de résilience contre la perte de données. Quand le nœud maître d’un tel cache échoue, le nœud réplica prend le relais pour traiter automatiquement les données. Ces machines virtuelles se trouvent sur des domaines d’erreur et de mise à jour distincts afin de réduire le risque qu’elles ne soient pas disponibles en même temps. Toutefois, en cas de défaillance majeure du centre de données, les machines virtuelles peuvent tomber en panne simultanément. Dans ces rares cas, vos données sont perdues.

Songez à utiliser la [persistance des données Redis](http://redis.io/topics/persistence) et la [géoréplication](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) pour renforcer la protection de vos données contre ces défaillances d’infrastructure.

## <a name="additional-information"></a>Informations supplémentaires

- [Résoudre les problèmes côté serveur liés à Azure Cache pour Redis](cache-troubleshoot-server.md)
- [Que sont les offres de cache Azure pour Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Surveillance du cache Azure pour Redis](cache-how-to-monitor.md)
- [Comment exécuter des commandes Redis ?](cache-faq.md#how-can-i-run-redis-commands)
