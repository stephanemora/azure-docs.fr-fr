---
title: Exemples du cache Azure pour Redis
description: 'Découvrez comment utiliser Azure Cache for Redis avec ces exemples de code : connexion à un cache, lecture et écriture de données dans un cache, fournisseurs Azure Cache for Redis ASP.NET.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 1fb63e8c537baee8a2b107a0b5451cc5b3e53bce
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109847850"
---
# <a name="azure-cache-for-redis-samples"></a>Exemples du cache Azure pour Redis
Vous trouverez dans cet article une liste d'exemples Azure Cache pour Redis. Les exemples couvrent des scénarios tels que : 

* Connexion à un cache
* Lecture et écriture de données vers et depuis un cache
* Utilisation des fournisseurs ASP.NET Azure Cache pour Redis 

Certains exemples sont des projets téléchargeables. D'autres contiennent des instructions pas à pas et incluent des extraits de code, mais ne sont pas liés à un projet téléchargeable.

## <a name="hello-world-samples"></a>Exemples Hello world
Les exemples de cette section illustrent les bases de la connexion à une instance d'Azure Cache pour Redis. L'exemple montre également comment lire et écrire des données dans le cache à l’aide de différents langages et clients Redis.

L'exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) montre comment effectuer différentes opérations de cache à l'aide du client .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

Cet exemple montre comment :

* Utiliser les différentes options de connexion
* Lire et écrire des objets dans le cache à l’aide des opérations synchrones et asynchrones
* Utiliser les commandes Redis MGET/MSET pour retourner les valeurs de clés spécifiées
* Effectuer des opérations Redis transactionnelles
* Utiliser des listes Redis et des ensembles triés
* Stocker des objets .NET à l’aide des sérialiseurs JsonConvert
* Utiliser les jeux Redis pour implémenter le balisage
* Travailler avec le Cluster Redis

Pour plus d'informations, consultez la documentation [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) sur GitHub. Pour accéder à d'autres scénarios d'usage, consultez les tests unitaires [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests).

L’exemple [How to use Azure Cache for Redis with Python](cache-python-get-started.md) montre comment utiliser le cache Azure pour Redis avec Python et le client [redis-py](https://github.com/andymccurdy/redis-py).

L'exemple [Utilisation d'objets .NET dans le cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) montre comment sérialiser des objets .NET afin de pouvoir les lire et les écrire depuis une instance d'Azure Cache pour Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utiliser le cache Azure pour Redis comme un fond de panier de montée en puissance pour ASP.NET SignalR
L'exemple [Utilisation d'Azure Cache pour Redis comme un fond de panier de scale-out pour ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) montre comment utiliser Azure Cache pour Redis comme fond de panier SignalR. Pour plus d’informations sur le fond de panier, consultez [SignalR Scaleout with Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Cache Azure pour Redis - exemple de requête client
Cet exemple compare les performances entre l'accès aux données à partir d'un cache et l'accès aux données à partir d'un espace de persistance. Cet exemple comporte deux projets.

* [Démonstration de l’amélioration des performances par la mise en cache des données avec le cache Azure pour Redis](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Amorcer la base de données et le cache pour la démonstration](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>État de session ASP.NET et mise en cache de la sortie
L'exemple [Utilisation d'Azure Cache pour Redis pour stocker ASP.net SessionState et OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) illustre ce qui suit :  

* Utilisation d'Azure Cache pour Redis pour stocker la session ASP.NET et le cache de sortie
* Utilisation des fournisseurs SessionState et OutputCache pour Redis

## <a name="manage-azure-cache-for-redis-with-maml"></a>Gérer le cache Azure pour Redis avec MAML
L'exemple [Gestion d'Azure Cache pour Redis à l'aide de bibliothèques de gestion Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) montre comment utiliser les bibliothèques de gestion Azure pour gérer (créer, mettre à jour et supprimer) votre cache. 

## <a name="custom-monitoring-sample"></a>Exemple de surveillance personnalisée
L'exemple [Accès aux données de surveillance d'Azure Cache pour Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) montre comment accéder aux données de surveillance de votre instance d'Azure Cache pour Redis en dehors du portail Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Un clone de style Twitter écrit avec PHP et Redis
L’exemple [Retwis](https://github.com/SyntaxC4-MSFT/retwis) est Redis Hello World. Il s'agit d'un clone minimal de réseau social de style Twitter écrit avec Redis et PHP, à l'aide du client [Predis](https://github.com/nrk/predis). Le code source est à la fois conçu pour être très simple et pour présenter les différentes structures de données Redis.

## <a name="bandwidth-monitor"></a>Surveiller la bande passante
L’exemple [Bandwidth monitor](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) vous permet de surveiller la bande passante utilisée sur le client. Pour mesurer la bande passante, exécutez l’exemple sur l’ordinateur client du cache, effectuez des appels au cache et observez la bande passante signalée par l’exemple de surveillance de la bande passante.
