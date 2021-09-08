---
title: Meilleures pratiques relatives pour le développement
titleSuffix: Azure Cache for Redis
description: Découvrez comment développer du code pour Azure Cache pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 1a91b5e11707d8f17effa6b2d711aaaa1a97afed
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116258"
---
# <a name="development"></a>Développement

## <a name="connection-resilience-and-server-load"></a>Résilience de la connexion et charge du serveur

Lorsque vous développez des applications clientes, veillez à prendre en compte les meilleures pratiques pertinentes pour la [résilience des connexions](cache-best-practices-connection.md) et la [gestion de la charge du serveur](cache-best-practices-server-load.md).

## <a name="consider-more-keys-and-smaller-values"></a>Prendre en compte d’autres clés et des valeurs plus petites

Redis fonctionne mieux avec des valeurs plus petites. Envisagez de diviser les plus grandes portions de données en plus petits segments pour répartir les données sur plusieurs clés. Pour plus d’informations sur la taille idéale des valeurs, consultez cet [article](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/).

Lisez avec attention les différents points exposés dans cette discussion à propos de Redis. Pour obtenir un exemple de problème qui peut être dû à de grandes valeurs, consultez [Grande demande ou taille de réponse](cache-troubleshoot-client.md#large-request-or-response-size).

## <a name="key-distribution"></a>Distribution de clés

Si vous envisagez d’utiliser le clustering Redis, lisez [Bonnes pratiques concernant le clustering Redis avec des clés](https://redislabs.com/blog/redis-clustering-best-practices-with-keys/).

## <a name="use-pipelining"></a>Utilisez le traitement en pipeline

Essayez de choisir un client Redis qui prend en charge le [traitement en pipeline Redis](https://redis.io/topics/pipelining). Le traitement en pipeline permet d’utiliser efficacement le réseau et d’obtenir le meilleur débit possible.

## <a name="avoid-expensive-operations"></a>Éviter les opérations coûteuses

Certaines opérations Redis, comme la commande [KEYS](https://redis.io/commands/keys), sont très coûteuses à exécuter et sont donc à proscrire. Pour plus d’informations sur les commandes à longue durée, consultez [Commandes de longue durée](cache-troubleshoot-server.md#long-running-commands)

## <a name="choose-an-appropriate-tier"></a>Choisir un niveau approprié
Utilisez le niveau Standard ou Premium pour les systèmes de production.  N’utilisez pas le niveau de base en production. Le niveau De base est un système à nœud unique, sans réplication des données ni contrat SLA. En outre, utilisez au moins un cache C1. Les caches C0 s’appliquent uniquement aux scénarios de développement/test simples, car :

- ils partagent un cœur de processeur
- ils utilisent peu de mémoire
- sont sujets à des problèmes de *voisinage bruyant*

Nous vous recommandons de tester les performances pour choisir le niveau approprié et valider les paramètres de connexion. Pour plus d’informations, consultez [Test de performances](cache-best-practices-performance.md).

## <a name="client-in-same-region-as-cache"></a>Client dans la même région que le cache

Placez votre instance de cache et votre application dans la même région. La connexion à un cache dans une autre région peut considérablement augmenter la latence et réduire la fiabilité.  

Vous pouvez vous connecter en dehors d’Azure, mais cela est déconseillé, *en particulier si vous utilisez Redis comme cache*.  Si vous utilisez le serveur Redis simplement comme magasin de clés/valeurs, la latence n’est sans doute pas votre première préoccupation.

## <a name="use-tls-encryption"></a>Utiliser le chiffrement TLS

Azure Cache pour Redis nécessite des communications chiffrées avec le protocole TLS par défaut. Les versions 1.0, 1.1 et 1.2 de TLS sont actuellement prises en charge. Toutefois, TLS 1.0 et 1.1 étant en passe de dépréciation dans l’ensemble du secteur, utilisez TLS 1.2 dans la mesure du possible.

Si votre outil ou bibliothèque de client ne prend pas en charge TLS, vous pouvez activer des connexions non chiffrées par le biais du [portail Azure](cache-configure.md#access-ports) ou des [API de gestion](/rest/api/redis/redis/update). Dans les cas où il est impossible d’établir des connexions chiffrées, nous vous recommandons de placer votre cache et votre application cliente dans un réseau virtuel. Pour plus d’informations sur les ports utilisés dans le scénario de mise en cache du réseau virtuel, consultez [ce tableau](cache-how-to-premium-vnet.md#outbound-port-requirements).

## <a name="client-library-specific-guidance"></a>Conseils pour la bibliothèque cliente

* [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
* [Java - Quel client dois-je utiliser ?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
* [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
* [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
* [Node.JS](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
* [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
* [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
* [Fournisseur d’état de session ASP.NET](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)
