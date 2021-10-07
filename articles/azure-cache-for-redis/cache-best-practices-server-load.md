---
title: Meilleures pratiques d’utilisation et de supervision de la charge du serveur
titleSuffix: Azure Cache for Redis
description: Découvrez comment utiliser et surveiller la charge de votre serveur pour Azure Cache pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 51a0a5ede1c9d978fcc7eea98c7519c70bd9126e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626130"
---
# <a name="manage-server-load-for-azure-cache-for-redis"></a>Gérer la charge du serveur pour Azure Cache pour Redis

## <a name="value-sizes"></a>Tailles de valeurs

La conception de votre application cliente détermine si vous devez stocker de nombreuses petites valeurs ou un plus petit nombre de valeurs plus élevées. Du point de vue du serveur Redis, les valeurs les plus petites donnent de meilleures performances. Nous vous recommandons de conserver une taille de valeur inférieure à 100 Ko.

Si votre conception exige que vous stockiez des valeurs plus élevées dans Azure Cache pour Redis, la charge du serveur sera plus élevée. Dans ce cas, vous devrez peut-être utiliser un niveau de cache plus élevé pour garantir que l’utilisation du processeur ne limite pas le débit.

Même si le cache dispose d’une capacité de processeur suffisante, les valeurs plus élevées augmentent les latences. Par conséquent, suivez les instructions dans [ Configurer des délais d’expiration appropriés](cache-best-practices-connection.md#configure-appropriate-timeouts).

Des valeurs plus élevées augmentent également les chances de fragmentation de la mémoire. Veillez donc à suivre les instructions dans [Configurer votre paramètre maxmemory-reserved](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting).

## <a name="avoid-client-connection-spikes"></a>Éviter les pics de connexion client

La création et la fermeture de connexions constituent une opération coûteuse pour le serveur Redis. Si votre application cliente crée ou ferme un trop grand nombre de connexions dans un laps de temps réduit, le serveur Redis peut être chargé.

Si vous instanciez de nombreuses instances clientes simultanément pour vous connecter à Redis, envisagez d’échelonner les nouvelles créations de connexions afin d’éviter un pic abrupt du nombre de clients connectés.

## <a name="memory-pressure"></a>Sollicitation de la mémoire

Une utilisation élevée de la mémoire sur le serveur rend le système plus susceptible de devoir paginer des données sur le disque, ce qui entraîne des défauts de page qui peuvent ralentir considérablement le système.

## <a name="avoid-long-running-commands"></a>Éviter les commandes de longue durée

Le serveur Redis est un système à thread unique. Les commandes de longue durée peuvent entraîner une latence ou des délais d’expiration côté client car le serveur ne peut pas répondre à d’autres requêtes lorsqu’il est occupé à travailler sur une commande de longue durée. Pour plus d’informations, consultez [Résoudre les problèmes côté serveur liés à Azure Cache pour Redis](cache-troubleshoot-server.md).  

## <a name="monitor-server-load"></a>Surveiller la charge du serveur

Ajoutez une surveillance de la charge du serveur pour vous assurer que vous recevez des notifications en cas de charge élevée du serveur. La surveillance peut vous aider à comprendre vos contraintes d’application. Vous pouvez ensuite travailler de manière proactive pour atténuer les problèmes. Nous vous recommandons d’essayer de conserver une charge du serveur inférieure à 80 % pour éviter des effets négatifs sur les performances.

## <a name="plan-for-server-maintenance"></a>Planifier la maintenance du serveur

Vérifiez que vous disposez d’une capacité de serveur suffisante pour gérer vos pics de charge lorsque vos serveurs de cache sont en cours de maintenance. Testez votre système en redémarrant les nœuds pendant les pics de charge. Pour plus d’informations sur la façon de simuler le déploiement d’un patch, consultez la section relative au [redémarrage](cache-administration.md#reboot).

## <a name="test-for-increased-server-load-after-failover"></a>Tester l’augmentation de la charge du serveur après le basculement

Pour les SKU Standard et Premium, chaque cache est hébergé sur deux nœuds. Un équilibreur de charge distribue les connexions clientes entre les deux nœuds. Lorsque la maintenance planifiée ou non planifiée se produit sur le nœud principal, le nœud met fin à toutes les connexions clientes. Dans ce cas, toutes les connexions clientes peuvent atterrir sur un seul nœud, ce qui entraîne une augmentation de la charge du serveur sur le nœud restant. Nous vous recommandons de tester ce scénario en redémarrant le nœud principal et en veillant à ce qu’un nœud puisse gérer toutes vos connexions clientes sans que la charge du serveur soit trop élevée.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes côté serveur liés à Azure Cache pour Redis](cache-troubleshoot-server.md)
- [Résilience des connexions](cache-best-practices-connection.md)
  - [Configurer les délais d’expiration appropriés](cache-best-practices-connection.md#configure-appropriate-timeouts).
- [Gestion de la mémoire](cache-best-practices-memory-management.md)
  - [Configurer le paramètre maxmemory-reserved](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
