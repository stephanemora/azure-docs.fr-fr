---
title: Meilleures pratiques pour les tests de performances
titleSuffix: Azure Cache for Redis
description: Tests de performances de Cache Azure pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 5d2152b6e1b2da165303c3a309ca97bf0262b5ee
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232776"
---
# <a name="performance-testing"></a>Tests de performances

1. Commencez par utiliser `redis-benchmark.exe` pour vérifier les caractéristiques générales du débit et de la latence de votre cache avant d’écrire vos propres tests de performances. Pour plus d’informations, consultez [Redis - Benchmark](#redis-benchmark-utility).

1. La machine virtuelle cliente utilisée pour le test doit se trouver *dans la même région* que votre instance de cache Redis.

1. Assurez-vous que la machine virtuelle cliente que vous utilisez *possède au moins autant de puissance de calcul et de bande passante* que le cache testé.

1. Il est important de veiller à ne tester les performances de votre cache que dans des conditions d’état stable. *Testez également les conditions de basculement*, et mesurez la charge UC/Serveur de votre cache pendant ce temps. Vous pouvez commencer un basculement en [redémarrant le nœud principal](cache-administration.md#reboot). Tester dans des conditions de basculement vous permet de voir comment votre application se comporte en matière de débit et de latence dans une situation de basculement. Un basculement peut se produire pendant des mises à jour et au cours d’un événement non planifié. Dans l’idéal, vous ne souhaitez pas voir un pic de charge UC/Serveur dépassant 80 %, même en cas de basculement, compte tenu de l’impact que cela peut avoir sur les performances.

1. Envisagez d’utiliser le Cache Azure de niveau Premium pour les instances Redis. Ces tailles de cache offrent une meilleure latence de réseau et un débit supérieur, car les instances s’exécutent sur un matériel plus performant pour le processeur et le réseau.

   > [!NOTE]
   > Les résultats observés sur les performances sont [publiés ici](./cache-planning-faq.yml#azure-cache-for-redis-performance) à titre de référence. Par ailleurs, sachez que SSL/TLS augmente la charge, et que vous pouvez donc constater des latences et/ou des débits différents si vous utilisez le chiffrement de transport.

## <a name="redis-benchmark-utility"></a>Redis - utilitaire benchmark

La documentation **Redis - utilitaire de point de référence** est [disponible ici](https://redis.io/topics/benchmarks).

`redis-benchmark.exe` ne prend pas en charge TLS. Vous devez [activer le port non-TLS via le portail](cache-configure.md#access-ports) avant d’exécuter le test. Une version de redis-benchmark.exe pour Windows est disponible [ici](https://github.com/MSOpenTech/redis/releases).

## <a name="redis-benchmark-examples"></a>Exemples Redis - benchmark

**Configuration de pré-test** : Préparez l’instance de cache avec les données requises pour les commandes de test de la latence et du débit :

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024
```

**Pour tester la latence** : Testez les requêtes GET avec une charge utile de 1 Ko :

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4
```

**Pour tester le débit :** Requêtes GET en pipeline avec une charge utile de 1 Ko :

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
```

## <a name="next-steps"></a>Étapes suivantes

- [Développement](cache-best-practices-development.md)
- [FAQ sur le développement d’Azure Cache pour Redis](cache-development-faq.yml)
- [Basculement et mise à jour corrective pour Azure Cache pour Redis](cache-failover.md)
