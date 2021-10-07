---
title: Mettre à l'échelle une instance Cache Redis Azure
description: Découvrez comment mettre à l’échelle vos instances Azure Cache pour Redis à l’aide du portail Azure et d’outils tels qu’Azure PowerShell et Azure CLI
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a1144cd0ccfe6857b88a29ea0f577d760f3addc6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431672"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Mettre à l'échelle une instance Cache Redis Azure

Le cache Azure pour Redis offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de cache en toute flexibilité. Après avoir créé un cache De base, Standard ou Premium, vous pouvez changer sa taille et son niveau pour répondre aux besoins de votre application. Cet article montre comment mettre à l’échelle votre cache à l’aide du portail Azure et d’outils tels qu’Azure PowerShell et Azure CLI.

## <a name="when-to-scale"></a>Quand mettre à l’échelle ?

Les fonctionnalités de [surveillance](cache-how-to-monitor.md) d’Azure Cache pour Redis permettent de surveiller l’intégrité et les performances de votre cache. Utilisez ces informations pour déterminer quand mettre à l’échelle ce cache.

Vous pouvez surveiller les mesures suivantes pour déterminer si vous avez besoin d’effectuer une mise à l’échelle.

- Charge du serveur Redis
  - Le serveur Redis est un processus monothread. Une charge élevée du serveur Redis signifie que le serveur n’est pas en mesure de suivre le rythme des demandes provenant de toutes les connexions clientes. Dans ce cas, il est utile d’activer le clustering ou d’augmenter le nombre de partitions afin de répartir les fonctions de surcharge entre les divers processus Redis. Le clustering et le plus grand nombre de partitions distribuent le chiffrement et le déchiffrement TLS et distribuent la connexion et la déconnexion TLS.
  - Pour plus d’informations, consultez [Configurer le clustering](cache-how-to-premium-clustering.md#set-up-clustering).
- Utilisation de la mémoire
  - Une utilisation élevée de mémoire indique que la taille de vos données est trop importante pour la taille actuelle du cache. Envisagez une mise à l’échelle vers une taille de cache avec plus de mémoire.
- Connexions clientes
  - Chaque taille de cache est limitée à un nombre de connexions clientes pouvant être prises en charge. Si vos connexions clientes sont proches de la limite de la taille du cache, envisagez d’effectuer un scale-up jusqu’à un niveau plus élevé ou un scale-out pour activer le clustering et augmenter le nombre de partitions. Votre choix dépend de l’utilisation de la mémoire et de la charge du serveur Redis.
  - Pour plus d’informations sur les limites de connexions par taille de cache, consultez [FAQ sur la planification d’Azure Cache pour Redis](./cache-planning-faq.yml).
- Bande passante réseau
  - Si le serveur Redis dépasse la bande passante disponible, les demandes client peuvent expirer parce que le serveur ne peut pas envoyer (push) les données au client suffisamment vite. Vérifiez les métriques « Lecture du cache » et « Écriture dans le cache » pour voir la bande passante utilisée côté serveur. Si votre serveur Redis dépasse la bande passante réseau disponible, vous devez envisager un scale-up vers une plus grande taille du cache avec une bande passante réseau plus large.
  - Pour plus d’informations sur la bande passante réseau disponible par taille de cache, consultez [FAQ sur la planification d’Azure Cache pour Redis](./cache-planning-faq.yml).

Si vous déterminez que votre cache ne répond plus aux besoins de votre application, vous pouvez passer à un niveau tarifaire de cache approprié pour votre application. Vous pouvez choisir un cache plus grand ou plus petit pour répondre à vos besoins.

Pour plus d’informations sur la façon de déterminer le niveau tarifaire de cache à utiliser, consultez [Choix du niveau approprié](cache-overview.md#choosing-the-right-tier) et [FAQ sur la planification d’Azure Cache pour Redis](./cache-planning-faq.yml).

## <a name="scale-a-cache"></a>Mise à l’échelle d’un cache

Pour mettre à l’échelle votre cache, [accédez au cache](cache-configure.md#configure-azure-cache-for-redis-settings) dans le [portail Azure](https://portal.azure.com), puis sélectionnez **Mise à l’échelle** sur la gauche.

:::image type="content" source="media/cache-how-to-scale/scale-a-cache.png" alt-text="mise à l’échelle dans le menu ressource":::

Choisissez un niveau tarifaire à droite, puis choisissez **Sélectionner**.

:::image type="content" source="media/cache-how-to-scale/select-a-tier.png" alt-text="Niveaux Azure Cache for Redis":::

Vous pouvez choisir un niveau tarifaire différent avec les restrictions suivantes :

- Vous ne pouvez pas passer d’un niveau de tarification supérieur à un niveau de tarification inférieur.
  - Vous ne pouvez pas passer d’un cache **Premium** à un cache **Standard** ou **De base**.
  - Vous ne pouvez pas passer d’un cache **Standard** à un cache **De base**.
- Vous pouvez passer d’un cache **De base** à un cache **Standard**, mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d’une autre taille, vous pouvez effectuer par la suite une opération de mise à l’échelle vers la taille voulue.
- Vous ne pouvez pas passer directement d’un cache **De base** à un cache **Premium**. Tout d’abord, passez du niveau **De base** au niveau **Standard** dans une opération de mise à l’échelle, puis du niveau **Standard** au niveau **Premium** dans l’opération de mise à l’échelle suivante.
- Vous ne pouvez pas mettre à l’échelle depuis une taille supérieure vers la taille **C0 (250 Mo)** . Toutefois, vous pouvez effectuer un scale-down vers toute autre taille dans le même niveau tarifaire. Par exemple, vous pouvez effectuer un scale-down de C5 Standard à C1 Standard.

Lorsque le cache est mis à l’échelle vers le nouveau niveau, une notification **Mise à l’échelle du Cache Redis** s’affiche.

:::image type="content" source="media/cache-how-to-scale/scaling-notification.png" alt-text="notification de mise à l’échelle":::

Une fois la mise à l’échelle terminée, le statut passe de **Mise à l’échelle** à **En cours d’exécution**.

## <a name="how-to-automate-a-scaling-operation"></a>Automatisation d’une opération de mise à l’échelle

Vous pouvez mettre à l’échelle vos instances de cache dans le portail Azure. Et vous pouvez opérer la mise à l’échelle à l’aide de cmdlets PowerShell, d’Azure CLI et des Bibliothèques de gestion Microsoft Azure (MAML).

- [Mise à l’échelle à l’aide de PowerShell](#scale-using-powershell)
- [Mise à l’échelle à l’aide de l’interface de ligne de commande Azure](#scale-using-azure-cli)
- [Mise à l’échelle à l’aide de MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Mise à l’échelle à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez mettre à l’échelle vos instances de cache Azure pour Redis avec PowerShell à l’aide de la cmdlet [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) lorsque les propriétés `Size`, `Sku` ou `ShardCount` sont modifiées. L’exemple suivant montre comment mettre à l’échelle un cache nommé `myCache` vers un cache de 2,5 Go.

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Pour plus d’informations sur la mise à l’échelle avec PowerShell, consultez [Pour mettre à l’échelle un cache Azure pour Redis à l’aide de PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Mise à l’échelle à l’aide de l’interface de ligne de commande Azure

Pour mettre à l’échelle vos instances Azure Cache pour Redis à l’aide de l’interface de ligne de commande Azure, appelez la commande `azure rediscache set` et transmettez les changements de configuration souhaités qui incluent une nouvelle taille, une référence (SKU) ou une taille de cluster, selon l’opération de mise à l’échelle choisie.

Pour plus d’informations sur la mise à l’échelle avec l’interface de ligne de commande Azure, consultez [Modification des paramètres d’un cache Azure pour Redis existant](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Mise à l’échelle à l’aide de MAML

Pour mettre à l’échelle vos instances du cache Azure pour Redis à l’aide des [Bibliothèques de gestion Microsoft Azure (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), appelez la méthode `IRedisOperations.CreateOrUpdate` et transmettez la nouvelle taille de `RedisProperties.SKU.Capacity`.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Pour plus d’informations, consultez l’exemple [Gestion du cache Azure pour Redis en utilisant les bibliothèques de gestion Microsoft Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) .

## <a name="scaling-faq"></a>FAQ sur la mise à l’échelle

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la mise à l’échelle du cache Azure pour Redis.

- [Puis-je mettre à l’échelle vers, depuis ou au sein d’un cache Premium ?](#can-i-scale-to-from-or-within-a-premium-cache)
- [Après la mise à l’échelle, dois-je modifier le nom ou les clés d’accès de mon cache ?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
- [Comment fonctionne la mise à l’échelle ?](#how-does-scaling-work)
- [Vais-je perdre mes données de cache durant la mise à l’échelle ?](#will-i-lose-data-from-my-cache-during-scaling)
- [Les paramètres personnalisés de mes bases de données sont-ils affectés au cours de la mise à l’échelle ?](#is-my-custom-databases-setting-affected-during-scaling)
- [Mon cache reste-t-il disponible durant la mise à l’échelle ?](#will-my-cache-be-available-during-scaling)
- [Existe-t-il des limitations de mise à l’échelle liées à la géoréplication ?](#are-there-scaling-limitations-with-geo-replication)
- [Opérations qui ne sont pas prises en charge](#operations-that-arent-supported)
- [Quelle est la durée d’une mise à l’échelle ?](#how-long-does-scaling-take)
- [Comment savoir quand la mise à l’échelle est terminée ?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Puis-je mettre à l’échelle vers, depuis ou au sein d’un cache Premium ?

- Vous ne pouvez pas passer d’un niveau tarifaire de cache **Premium** à un niveau tarifaire **De base** ou **Standard**.
- Vous pouvez passer d’un niveau tarifaire de cache **Premium** à un autre.
- Vous ne pouvez pas passer directement d’un cache **De base** à un cache **Premium**. Tout d’abord, passez du niveau **De base** au niveau **Standard** dans une opération de mise à l’échelle, puis du niveau **Standard** au niveau **Premium** dans une opération de mise à l’échelle ultérieure.
- Si vous avez activé le clustering lorsque vous avez créé votre cache **Premium** , vous pouvez [changer la taille du cluster](cache-how-to-premium-clustering.md#cluster-size). Si votre cache a été créé sans que le clustering soit activé, vous pourrez configurer le clustering par la suite.

Pour plus d’informations, consultez la page [Comment configurer le clustering Redis pour un cache Azure pour Redis Premium](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Après la mise à l’échelle, dois-je modifier le nom ou les clés d’accès de mon cache ?

Non, le nom et les clés d’accès de votre cache n’ont pas à être modifiés durant une opération de mise à l’échelle.

### <a name="how-does-scaling-work"></a>Comment fonctionne la mise à l’échelle ?

- Quand vous mettez à l’échelle un cache **De base** vers une taille différente, celui-ci est arrêté et un nouveau cache est provisionné avec la nouvelle taille. Pendant ce temps, le cache n’est pas disponible et toutes les données qu’il contenait sont perdues.
- Quand vous mettez à l’échelle un cache **De base** vers un cache **Standard**, un cache de réplica est provisionné et les données sont copiées du cache principal vers le cache de réplica. Le cache reste disponible pendant le processus de mise à l'échelle.
- Quand vous mettez à l’échelle un cache **Standard** vers une taille différente ou vers un cache **Premium**, l’un des réplicas est arrêté et reprovisionné avec la nouvelle taille et les données sont transférées. L’autre réplica effectue ensuite un basculement avant d’être reprovisionné, selon un processus similaire à celui intervenant lors d’une défaillance d’un des nœuds du cache.
- Quand vous effectuez un scale-out d’un cache en cluster, de nouvelles partitions sont provisionnées et ajoutées au cluster de serveurs Redis. Les données sont ensuite repartitionnées entre toutes les partitions.
- Quand vous effectuez un scale-in d’un cache en cluster, les données sont d’abord repartitionnées, puis la taille du cluster est réduite au nombre de partitions requis.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Vais-je perdre mes données de cache durant la mise à l’échelle ?

- Quand vous mettez à l’échelle un cache **De base** vers une nouvelle taille, toutes les données sont perdues et le cache n’est plus disponible pendant l’opération de mise à l’échelle.
- Quand vous mettez à l’échelle un cache **De base** vers un cache **Standard**, les données qui se trouvent dans le cache sont généralement conservées.
- Quand vous mettez à l’échelle un cache **Standard** vers une plus grande taille ou un niveau supérieur, ou un cache **Premium** vers une plus grande taille, toutes les données sont généralement conservées. Quand vous mettez à l’échelle un cache Standard ou Premium vers une plus petite taille, des données peuvent se perdre lors du scale-down, si la taille des données dépasse la nouvelle taille réduite. En cas de pertes de données lors de la descente en puissante, les clés sont supprimées à l'aide de la stratégie d’éviction [allkeys-lru](https://redis.io/topics/lru-cache) .

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Les paramètres personnalisés de mes bases de données sont-ils affectés au cours de la mise à l’échelle ?

Si vous avez configuré une valeur personnalisée pour le paramètre `databases` lors de la création du cache, n’oubliez pas que certains niveaux de tarification appliquent des [limites des bases de données](cache-configure.md#databases) différentes. Voici quelques considérations relatives à la mise à l’échelle dans le cadre de ce scénario :

- Lors d’une mise à l’échelle vers un niveau de tarification associé à une limite `databases` plus faible que le niveau de tarification actuel :
  - Si vous utilisez le nombre par défaut de `databases`, soit 16 pour tous les niveaux tarifaires, aucune donnée n’est perdue.
  - Si vous utilisez un nombre personnalisé de `databases` qui s’inscrit dans les limites du niveau vers lequel vous effectuez la mise à l’échelle, ce paramètre `databases` est conservé et aucune donnée n’est perdue.
  - Si vous utilisez un nombre de `databases` personnalisé qui dépasse les limites du nouveau niveau, le paramètre `databases` est réduit aux limites de celui-ci, et toutes les données contenues dans les bases de données supprimées sont perdues.
- Lors d’une mise à l’échelle vers un niveau tarifaire assorti d’une limite de `databases` identique ou supérieure à celle du niveau actuel, votre paramètre `databases` est conservé et aucune donnée n’est perdue.

Si les caches Standard et Premium bénéficient d’une garantie de disponibilité à 99,9 % adossée à un contrat SLA, aucun contrat de ce type ne couvre la perte de données.

### <a name="will-my-cache-be-available-during-scaling"></a>Mon cache reste-t-il disponible durant la mise à l’échelle ?

- Les caches **Standard** et **Premium** restent disponibles pendant l’opération de mise à l’échelle. Toutefois, des spots de connexion peuvent se produire lors de la mise à l’échelle des caches standard vers des caches premium, ainsi que des caches de base vers des caches standard. Ces sauts de connexion sont censés être brefs et des clients Redis peuvent généralement rétablir leur connexion instantanément.
- Les caches **de base** sont hors connexion pendant les opérations de mise à l’échelle vers une taille différente. Les caches De base restent disponibles lors de la mise à l’échelle du niveau **De base** au niveau **Standard**, mais peuvent subir une légère interruption de connexion. Si une interruption de connexion se produit, les clients Redis peuvent généralement rétablir leur connexion instantanément.

### <a name="are-there-scaling-limitations-with-geo-replication"></a>Existe-t-il des limitations de mise à l’échelle liées à la géoréplication ?

Quand la géoréplication est configurée, vous pouvez remarquer que vous ne pouvez pas mettre à l’échelle un cache ni modifier le nombre de partitions dans un cluster. Un lien de géoréplication entre deux caches vous empêche de mettre à l’échelle le fonctionnement et de changer le nombre de partitions dans un cluster. Il vous faudra supprimer le lien du cache pour pouvoir émettre ces commandes. Pour plus d’informations, consultez la page [Configurer la géoréplication](cache-how-to-geo-replication.md).

### <a name="operations-that-arent-supported"></a>Opérations qui ne sont pas prises en charge

- Vous ne pouvez pas passer d’un niveau de tarification supérieur à un niveau de tarification inférieur.
  - Vous ne pouvez pas passer d’un cache **Premium** à un cache **Standard** ou **De base**.
  - Vous ne pouvez pas passer d’un cache **Standard** à un cache **De base**.
- Vous pouvez passer d’un cache **De base** à un cache **Standard**, mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d’une autre taille, vous pouvez effectuer une opération de mise à l’échelle vers la taille voulue par la suite.
- Vous ne pouvez pas passer directement d’un cache **De base** à un cache **Premium**. Passez d’abord du niveau **De base** au niveau **Standard** en une opération de mise à l’échelle, puis du niveau **Standard** au niveau **Premium** en une opération ultérieure.
- Vous ne pouvez pas mettre à l’échelle depuis une taille supérieure vers la taille **C0 (250 Mo)** .

En cas d’échec d’une opération de mise à l’échelle, le service essaie d’annuler l’opération et le cache reviendra à sa taille d’origine.

### <a name="how-long-does-scaling-take"></a>Quelle est la durée d’une mise à l’échelle ?

Le temps de mise à l’échelle dépend de plusieurs facteurs. Voici quelques facteurs qui peuvent affecter le temps nécessaire à une mise à l’échelle.

- Quantité de données : plus la quantité de données est importante et plus leur réplication est longue.
- Nombre élevé de demandes d’écriture : un nombre plus élevé d’écritures signifie que plus de données sont répliquées entre les nœuds et les partitions.
- Charge de serveur élevée : une charge de serveur supérieure signifie que le serveur Redis est occupé et qu’il dispose d’un nombre limité de cycles processeur pour effectuer la redistribution des données.

En règle générale, lorsque vous mettez à l’échelle un cache sans données, l’opération prend environ 20 minutes. Pour les caches clusterisés, la mise à l’échelle prend environ 20 minutes par partition avec des données minimales.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Comment savoir quand la mise à l’échelle est terminée ?

Le déroulement de l’opération de mise à l’échelle est affiché dans le portail Azure. Une fois la mise à l’échelle terminée, le statut passe à **En cours d’exécution**.
