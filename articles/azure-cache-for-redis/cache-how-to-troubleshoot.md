---
title: Résolution des problèmes du cache Azure pour Redis | Microsoft Docs
description: Découvrez comment résoudre les problèmes courants liés au cache Azure pour Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60830005"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Résolution des problèmes du cache Azure pour Redis

Cet article a pour but de vous aider à résoudre les divers types de problèmes que vous pouvez rencontrer lorsque vous vous connectez au Cache Azure Redis.

- [La résolution des problèmes côté client](#client-side-troubleshooting) vous permet d’identifier et de résoudre les problèmes qui se produisent dans l’application qui se connecte à votre cache.
- [La résolution des problèmes côté serveur](#server-side-troubleshooting) vous permet d’identifier et de résoudre les problèmes qui se produisent dans le Cache Azure Redis.
- [La résolution des problèmes de perte de données](#data-loss-troubleshooting) vous permet d’identifier et de résoudre les incidents liés à des clés introuvables dans le cache.
- [Exceptions liées au délai d’expiration de StackExchange.Redis](#stackexchangeredis-timeout-exceptions) : cette section explique comment résoudre les problèmes relatifs à la bibliothèque StackExchange.Redis.

> [!NOTE]
> Dans ce guide, plusieurs procédures de résolution de problèmes comprennent des instructions pour exécuter des commandes Redis et surveiller diverses mesures de performances. Pour plus d’informations et d’instructions, consultez les articles dans la section [Informations supplémentaires](#additional-information) .
>
>

## <a name="client-side-troubleshooting"></a>Résolution des problèmes côté client

Cette section traite des problèmes qui surviennent à cause d’un souci sur l’application cliente.

- [Saturation de la mémoire sur le client](#memory-pressure-on-the-client)
- [Augmentation de trafic](#burst-of-traffic)
- [Utilisation importante du processeur du client](#high-client-cpu-usage)
- [Bande passante dépassée côté client](#client-side-bandwidth-exceeded)
- [Taille importante de la demande/réponse](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Saturation de la mémoire sur le client

La sollicitation de la mémoire sur l’ordinateur client génère toutes sortes de problèmes de performances susceptibles de retarder le traitement des réponses provenant du cache. Lorsque la mémoire est trop sollicitée, le système peut paginer les données vers le disque. Cette _pagination_ ralentit considérablement le système.

Pour détecter une sollicitation de la mémoire sur le client :

- Supervisez l’utilisation de la mémoire sur l’ordinateur et vérifiez qu’elle ne dépasse pas la mémoire disponible.
- Supervisez le compteur de performances `Page Faults/Sec` du client. Lors d’un fonctionnement normal, la plupart des systèmes peuvent rencontrer des défauts de page. Les pics de défauts de page correspondant à des délais d’attente de requêtes peuvent indiquer une sollicitation de la mémoire.

Vous pouvez réduire la sursollicitation de la mémoire sur le client de plusieurs façons :

- Analysez vos modèles d’utilisation de la mémoire pour réduire la consommation de mémoire sur le client.
- Mettez à niveau votre machine virtuelle cliente pour bénéficier de davantage de mémoire.

### <a name="burst-of-traffic"></a>Augmentation de trafic

Les augmentations de trafic combinées à des paramètres `ThreadPool` insatisfaisants peuvent retarder le traitement des données déjà envoyées par le serveur Redis, mais pas encore consommées côté client.

Supervisez la façon dont évoluent vos statistiques `ThreadPool` au fil du temps à l’aide d’un [exemple `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Pour une analyse plus approfondie, vous pouvez utiliser les messages `TimeoutException` de StackExchange.Redis comme ci-dessous :

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Dans l’exception précédente, plusieurs problèmes sont intéressants :

- Notez que dans les sections `IOCP` et `WORKER`, la valeur de `Busy` est supérieure à la valeur de `Min`. Cette différence signifie que vos paramètres `ThreadPool` ont besoin d’être ajustés.
- Vous pouvez également voir `in: 64221`. Cette valeur indique que 64 211 octets ont été reçus au niveau de la couche de socket du noyau du client, mais qu’ils n’ont pas encore été lus par l’application. En général, cette différence signifie que votre application (par exemple, StackExchange.Redis) ne lit pas les données du réseau aussi rapidement que le serveur les lui envoie.

Vous pouvez [configurer vos paramètres `ThreadPool`](https://gist.github.com/JonCole/e65411214030f0d823cb) pour que votre pool de threads puisse rapidement faire l’objet d’un scale-up en cas d’augmentation du trafic.

### <a name="high-client-cpu-usage"></a>Utilisation importante du processeur du client

Une utilisation élevée du processeur du client indique que le système ne peut pas exécuter la charge de travail qui lui a été ajoutée dans un délai normal. Même si le cache a envoyé la réponse rapidement, le client ne peut pas traiter la réponse en temps voulu.

Supervisez l’utilisation du processeur du client à l’échelle du système à l’aide des métriques qui sont disponibles dans le portail Azure ou via les compteurs de performances de l’ordinateur. Ne tenez pas compte du processeur de *processus*, car le processeur peut très bien être faiblement utilisé par un processus tout en étant fortement sollicité à l’échelle du système. Regardez les pics d’utilisation du processeur qui correspondent à des délais d’expiration. Lorsque le processeur est très utilisé, vous pouvez également voir s’afficher des valeurs `in: XXX` élevées dans les messages d’erreur `TimeoutException`, comme ceux décrits dans la section [Augmentation de trafic](#burst-of-traffic).

> [!NOTE]
> StackExchange.Redis 1.1.603 et versions ultérieures inclut la mesure `local-cpu` dans les messages d’erreur `TimeoutException`. Vérifiez que vous utilisez la dernière version en date du [package NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Nous corrigeons en permanence le code pour le rendre plus robuste aux délais d’expiration. Il est donc primordial d’utiliser la dernière version.
>
>

Pour remédier à une utilisation élevée du processeur d’un client :

- Recherchez la cause des pics d’utilisation du processeur.
- Mettez à niveau votre machine virtuelle cliente pour bénéficier d’une plus grande capacité de processeur.

### <a name="client-side-bandwidth-exceeded"></a>Bande passante dépassée côté client

Selon l’architecture des ordinateurs clients, ces ordinateurs peuvent afficher des limites concernant la bande passante réseau qu’ils peuvent utiliser. Si le client dépasse la bande passante disponible en surchargeant la capacité du réseau, les données ne sont pas traitées côté client aussi rapidement que le serveur les envoie. Cette situation peut entraîner des délais d’attente.

Supervisez la façon dont votre utilisation de la bande passante évolue au fil du temps, à l’aide d’un [exemple `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ce code peut ne pas s’exécuter correctement dans certains environnements aux autorisations restreintes (tels que les sites web Azure).

Réduisez l’utilisation de la bande passante réseau ou augmentez la taille de la machine virtuelle cliente pour bénéficier d’une plus grande capacité réseau.

### <a name="large-requestresponse-size"></a>Taille importante de la demande/réponse

Une demande/réponse volumineuse peut entraîner des délais d’expiration. Par exemple, supposons que la durée du délai d’expiration que vous avez configurée sur votre client soit de 1 seconde. Votre application demande deux clés (par exemple, « A » et « B ») en même temps (à l’aide de la même connexion réseau physique). La plupart des clients prennent en charge le traitement en pipeline des requêtes, de sorte que les deux requêtes A et B sont envoyées l’une après l’autre sans attendre les réponses. Le serveur renvoie les réponses dans le même ordre. Si la réponse A est volumineuse, elle peut consommer la majeure partie du délai d’expiration des requêtes suivantes.

Dans l’exemple suivant, les requêtes A et B sont envoyées rapidement au serveur. Le serveur commence rapidement à envoyer les réponses A et B. En raison des temps de transfert de données, la réponse B doit attendre l’expiration de la réponse A, même si le serveur a répondu rapidement.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Cette demande/réponse est difficile à mesurer. Vous pouvez instrumenter votre code client pour suivre les requêtes et les réponses volumineuses.

Les solutions possibles pour la gestion des réponses volumineuses sont variées, et incluent notamment :

1. Optimiser votre application pour prendre en charge un grand nombre de petites valeurs plutôt qu’un petit nombre de grandes valeurs
    - La meilleure solution consiste à diviser vos données en valeurs plus petites.
    - Lisez le billet [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) pour savoir pourquoi il est recommandé d’utiliser des valeurs moins élevées.
1. Augmenter la taille de votre machine virtuelle pour obtenir des capacités de bande passante plus élevées
    - Une plus grande quantité de bande passante sur votre machine virtuelle cliente ou serveur peut réduire le temps de transfert des données pour les réponses volumineuses.
    - Comparez l’utilisation actuelle du réseau par les deux ordinateurs aux limites associées à la taille de votre machine virtuelle. L’augmentation de la bande passante sur le serveur ou le client uniquement peut ne pas suffire.
1. Augmenter le nombre d’objets de connexion qu’utilise votre application
    - Utilisez un tourniquet (round-robin) pour envoyer des requêtes vers différents objets de connexion.

## <a name="server-side-troubleshooting"></a>Résolution des problèmes côté serveur

Cette section traite des problèmes qui surviennent en raison d’un souci sur le serveur du cache.

- [Saturation de la mémoire sur le serveur](#memory-pressure-on-the-server)
- [Utilisation élevée du processeur / charge importante du serveur](#high-cpu-usage--server-load)
- [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Saturation de la mémoire sur le serveur

La saturation de la mémoire sur le serveur génère toutes sortes de problèmes de performance susceptibles de retarder le traitement des demandes. Lorsque la mémoire est trop sollicitée, le système peut paginer les données vers le disque. Cette _pagination_ ralentit considérablement le système. Plusieurs causes peuvent être à l’origine de cette saturation de la mémoire :

- Le cache contient beaucoup de données et il est sur le point d’atteindre sa capacité maximale.
- Redis constate une importante fragmentation de mémoire. Cette fragmentation est plus souvent provoquée par le stockage d’objets volumineux, car Redis est optimisé pour les petits objets.

Redis expose deux statistiques via la commande [INFO](https://redis.io/commands/info) qui peuvent vous aider à identifier ce problème : « used_memory » et « used_memory_rss ». Vous pouvez [afficher ces métriques](cache-how-to-monitor.md#view-metrics-with-azure-monitor) à l’aide du portail.

Vous pouvez apporter plusieurs modifications pour maintenir une utilisation correcte de la mémoire :

- [Configurez une stratégie de mémoire](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) et définissez des délais d’expiration sur vos clés. Cette stratégie peut ne pas suffire si vous constatez une fragmentation.
- [Configurez une valeur maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) suffisamment grande pour compenser la fragmentation de la mémoire. Pour plus d’informations, consultez les [considérations relatives aux réservations de mémoire](#considerations-for-memory-reservations) ci-dessous.
- Divisez vos objets volumineux mis en cache en objets plus petits.
- [Créez des alertes](cache-how-to-monitor.md#alerts) pour des métriques comme la mémoire utilisée, afin d’être averti le plus tôt possible des impacts potentiels.
- [Effectuez une mise à l’échelle](cache-how-to-scale.md) pour une plus grande taille de cache et une plus grande capacité de mémoire.

#### <a name="considerations-for-memory-reservations"></a>Considérations relatives aux réservations de mémoire

La mise à jour des valeurs de réservation de mémoire, telles que maxmemory-reserved, peut affecter les performances du cache. Supposons que vous ayez un cache de 53 Go comprenant 49 Go de données. Si vous définissez la valeur de réservation sur 8 Go, cela réduit la mémoire maximale disponible du système à 45 Go. Si les valeurs _used_memory_ ou _used_memory_rss_ sont supérieures à 45 Go, le système peut supprimer des données jusqu’à ce que _used_memory_ et _used_memory_rss_ soient inférieures à 45 Go. La suppression de données peut augmenter la fragmentation de la charge et de la mémoire du serveur.

### <a name="high-cpu-usage--server-load"></a>Utilisation élevée du processeur / charge importante du serveur

Une charge serveur ou une utilisation du processeur élevées signifie que le serveur ne peut pas traiter les requêtes en temps voulu. Le serveur peut être lent et ne pas être capable de répondre aux requêtes en temps voulu.

[Supervisez les métriques](cache-how-to-monitor.md#view-metrics-with-azure-monitor) comme l’utilisation du processeur ou la charge serveur. Regardez les pics d’utilisation du processeur qui correspondent à des délais d’expiration.

Plusieurs modifications sont possibles pour réduire la charge serveur :

- Recherchez la cause des pics d’utilisation du processeur, comme l’exécution de [commandes consommant beaucoup de ressources](#expensive-commands) ou les défauts de page dus à une trop grande sollicitation de la mémoire.
- [Créez des alertes](cache-how-to-monitor.md#alerts) pour des métriques comme l’utilisation du processus ou la charge serveur, afin d’être averti le plus tôt possible des impacts potentiels.
- [Effectuez une mise à l’échelle](cache-how-to-scale.md) pour une plus grande taille de cache et une plus grande capacité de processeur.

#### <a name="expensive-commands"></a>Commandes consommant beaucoup de ressources

Toutes les commandes Redis ne sont pas égales : certaines sont plus coûteuses à exécuter que d’autres. La [documentation concernant les commandes Redis](https://redis.io/commands) montre la complexité temporelle de chaque commande. Il est recommandé de passer en revue les commandes que vous exécutez sur votre cache afin de bien comprendre leur impact sur les performances. Par exemple, les utilisateurs se servent souvent de la commande [KEYS](https://redis.io/commands/keys) sans savoir qu’il s’agit d’une opération O(N). Vous pouvez éviter la commande KEYS en utilisant [SCAN](https://redis.io/commands/scan) pour réduire les pics d’utilisation du processeur.

Avec la commande [SLOWLOG](https://redis.io/commands/slowlog), vous pouvez mesurer les commandes consommant beaucoup de ressources qui sont actuellement exécutées sur le serveur.

### <a name="server-side-bandwidth-exceeded"></a>Bande passante dépassée côté serveur

La capacité de la bande passante réseau varie selon la taille du cache. Si le serveur dépasse la bande passante disponible, les données ne sont pas envoyées au client aussi rapidement. Les requêtes du client risquent d’expirer, car le serveur ne peut pas envoyer (push) les données au client suffisamment rapidement.

Vous pouvez vous servir des métriques « Lecture du cache » et « Écriture dans le cache » pour voir la quantité de bande passante qui est utilisée côté serveur. Vous pouvez [afficher ces métriques](cache-how-to-monitor.md#view-metrics-with-azure-monitor) dans le portail.

Pour réduire une utilisation de la bande passante réseau proche de la capacité maximale :

- Modifiez le comportement d’appel du client afin de réduire la demande réseau.
- [Créez des alertes](cache-how-to-monitor.md#alerts) pour des métriques comme la lecture du cache ou l’écriture dans le cache, afin d’être averti le plus tôt possible des impacts potentiels.
- [Effectuez une mise à l’échelle](cache-how-to-scale.md) pour une plus grande taille de cache et une plus grande capacité de bande passante réseau.

## <a name="data-loss-troubleshooting"></a>Résolution des problèmes de perte de données

Je m’attendais à trouver certaines données dans mon instance du cache Azure pour Redis, mais elles semblent absentes.

Consultez la page [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) pour identifier les causes et solutions possibles.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceptions au délai d’expiration de StackExchange.Redis

Pour les opérations synchrones, StackExchange.Redis utilise un paramètre de configuration appelé `synctimeout`, qui a une valeur par défaut de 1 000 ms. Si un appel synchrone ne se termine pas en temps voulu, le client StackExchange.Redis génère une erreur de délai d’expiration similaire à la suivante :

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Ce message d’erreur contient des mesures qui peuvent vous aider à identifier la cause du problème et à en trouver la solution. Le tableau suivant contient des détails sur les mesures des messages d’erreur.

| Mesure de message d’erreur | Détails |
| --- | --- |
| inst |Au cours du dernier intervalle de temps : 0 commandes ont été émises |
| mgr |Le gestionnaire de sockets fait un `socket.select`, ce qui signifie qu’il demande au système d’exploitation de désigner un socket qui a quelque chose à faire. Le lecteur ne lit pas activement ce qui se trouve sur le réseau, car il pense qu’il n’y a rien à faire. |
| file d'attente |73 opérations sont en cours. |
| qu |6 des opérations en cours se trouvent dans la file d’attente et n’ont pas encore été envoyées au réseau sortant. |
| qs |67 des opérations en cours ont été envoyées au serveur, mais aucune réponse n’est encore disponible. La réponse peut être `Not yet sent by the server` ou `sent by the server but not yet processed by the client.`. |
| qc |Aucune des opérations en cours n’a encore reçu de réponse, mais celles-ci sont marquées comme terminées, car elles attendent dans la boucle d’exécution. |
| wr |Il y a un enregistreur actif (ce qui signifie que les 6 requêtes en attente ne sont pas ignorées) bytes/activewriters |
| commencer |Aucun lecteur n’est actif et aucun octet n’est disponible en lecture sur la carte réseau octets/lecteurs actifs |

### <a name="steps-to-investigate"></a>Procédure d’analyse

1. Il est recommandé de suivre la procédure ci-dessous pour se connecter, en cas d’utilisation du client StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Pour plus d’informations, consultez [Utilisation du Cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Vérifiez que le serveur et l’application cliente se trouvent dans la même région Azure. Par exemple, vous pouvez être confronté à des expirations si votre cache se trouve dans la région USA Est, alors que le client se trouve dans la région USA Ouest et que la requête n’est pas traitée dans l’intervalle `synctimeout`. Vous pouvez également être confronté à des expirations lorsque vous procédez à un débogage sur votre ordinateur de développement local. 

    Il est vivement recommandé de placer le cache et le client dans la même région Azure. Si votre implémentation nécessite des appels interrégionaux, vous devez régler l’intervalle `synctimeout` sur une valeur supérieure à la valeur par défaut (1000 ms), en incluant une propriété `synctimeout` dans la chaîne de connexion. L’exemple suivant montre un extrait de chaîne de connexion pour StackExchange.Redis qui est fournie au cache Azure pour Redis avec une valeur `synctimeout` de 2 000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Vérifiez que vous utilisez la dernière version en date du [package NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Nous corrigeons en permanence le code pour le rendre plus robuste aux délais d’expiration. Il est donc primordial d’utiliser la dernière version.
1. Si des requêtes sont impactées par les limitations de bande passante sur le serveur ou le client, leur traitement prend davantage de temps, ce qui entraîne des expirations. Pour savoir si l’expiration est due à la bande passante réseau du serveur, consultez [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded). Pour savoir si l’expiration est due à la bande passante réseau du client, consultez [Bande passante dépassée côté client](#client-side-bandwidth-exceeded).
1. Constatez-vous une utilisation intensive du processeur sur le serveur ou sur le client ?

   - Vérifiez si le processeur est utilisé de manière intensive sur le client. Une utilisation élevée du processeur peut empêcher la requête d’être traitée dans l’intervalle `synctimeout` et entraîner son expiration. Augmentez la taille de votre client ou répartissez la charge pour résoudre ce problème.
   - Vérifiez si le processeur est utilisé de manière intensive sur le serveur, à l’aide de la [métrique de performance du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `CPU`. Si des requêtes sont reçues pendant que Redis utilise intensivement le processeur, ces requêtes peuvent expirer. Pour résoudre ce problème, vous pouvez répartir la charge sur plusieurs partitions dans un cache premium, ou effectuer une mise à niveau vers une taille ou un niveau de tarification supérieur. Pour plus d’informations, consultez [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded).
1. Le traitement de certaines commandes est-il trop long sur le serveur ? Les commandes dont l’exécution prend beaucoup de temps sur le serveur Redis peuvent entraîner des expirations. Pour plus d’informations sur les commandes à exécution longue, consultez [Commandes consommant beaucoup de ressources](#expensive-commands). Vous pouvez vous connecter à votre instance du Cache Azure pour Redis à l’aide du client redis-cli ou de la [console Redis](cache-configure.md#redis-console). Ensuite, exécutez la commande [SLOWLOG](https://redis.io/commands/slowlog) pour voir si l’exécution des requêtes est plus lente que d’habitude. Le serveur Redis et StackExchange.Redis sont optimisés pour traiter un grand nombre de petites requêtes plutôt qu’un nombre réduit de demandes volumineuses. Fractionner vos données en segments plus petits peut améliorer les choses.

    Pour plus d’informations sur la connexion au point de terminaison SSL du cache à l’aide de redis-cli et de stunnel, consultez le billet de blog [Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Une charge élevée du serveur Redis peut provoquer des délais d’expiration. Vous pouvez surveiller la charge du serveur à l’aide de la `Redis Server Load` [mesure des performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Une charge serveur de 100 (valeur maximale) signifie que le serveur redis a été occupé à plein temps à traiter des demandes. Pour voir si certaines demandes monopolisent les capacités du serveur, exécutez la commande SlowLog comme indiqué dans le paragraphe précédent. Pour plus d’informations, consultez Utilisation élevée du processeur / charge importante du serveur.
1. Un autre événement côté client a-t-il pu causer un blocage du réseau ? Les événements courants incluent notamment le scale-up et le scale-down du nombre d’instances de client, le déploiement de nouvelles versions du client et la mise à l’échelle automatique. Durant nos tests, nous avons découvert que la mise à l’échelle automatique ainsi que le scale-up et le scale-down pouvaient entraîner une perte de la connectivité réseau sortante pendant plusieurs secondes. Le code de StackExchange.Redis résiste à de tels événements et rétablit la connexion. Pendant la reconnexion, les requêtes qui se trouvent dans la file d’attente peuvent expirer.
1. Dans le cache, une requête volumineuse précédant plusieurs petites requêtes a-t-elle expiré ? Le paramètre `qs` du message d’erreur vous indique le nombre de requêtes envoyées du client au serveur, mais qui n’ont pas encore traité de réponse. Cette valeur peut continuer d’augmenter car StackExchange.Redis utilise une seule connexion TCP et ne peut lire qu’une réponse à la fois. Même si la première opération a expiré, cela n’empêche pas l’envoi d’autres données vers ou à partir du serveur. Les autres requêtes sont donc bloquées jusqu’à ce que la requête volumineuse ait été traitée, ce qui peut entraîner des expirations. Une solution pour réduire le risque de délais d’expiration consiste à vérifier que le cache est assez grand pour votre charge de travail et à fractionner les valeurs importantes en valeurs plus petites. Une autre solution consiste à utiliser un pool d’objets `ConnectionMultiplexer` dans votre client et à choisir le `ConnectionMultiplexer` le moins chargé lors de l’envoi d’une nouvelle demande. Un chargement sur plusieurs objets de connexion permet d’empêcher une expiration de faire expirer les autres requêtes.
1. Si vous utilisez `RedisSessionStateProvider`, vérifiez que vous avez configuré correctement le délai d’expiration de nouvelle tentative. `retryTimeoutInMilliseconds` doit être supérieur à `operationTimeoutInMilliseconds`, sans quoi aucune nouvelle tentative ne se produit. Dans l’exemple suivant, `retryTimeoutInMilliseconds` est réglé sur 3 000. Pour plus d’informations, consultez la page [Fournisseur d’état de session ASP.NET pour cache Azure pour Redis](cache-aspnet-session-state-provider.md) et [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Comment utiliser les paramètres de configuration du fournisseur d’état de session et du fournisseur de caches de sortie).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Vérifiez l’utilisation de la mémoire sur le serveur du cache Azure pour Redis en [surveillant](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` et `Used Memory`. Si une stratégie d’éviction est en place, Redis commence la suppression de clés lorsque `Used_Memory` atteint la taille du cache. Dans l’idéal, `Used Memory RSS` doit être légèrement supérieur à `Used memory`. Une différence importante traduit une fragmentation de la mémoire (interne ou externe). Lorsque `Used Memory RSS` est inférieur à `Used Memory`, cela signifie que le système d’exploitation a permuté une partie de la mémoire cache. Si cette permutation se produit, vous pouvez rencontrer des latences importantes. Comme Redis ne contrôle pas le mode de mappage de ses allocations aux pages mémoire, une valeur élevée de `Used Memory RSS` traduit souvent un pic d’utilisation de la mémoire. Lorsque le serveur Redis libère de la mémoire, l’allocateur prend de la mémoire, mais il peut ne pas la rendre au système. Il peut y avoir une différence entre la valeur de `Used Memory` et la consommation de mémoire telle qu’elle est indiquée par le système d’exploitation. La mémoire a peut-être été utilisée et publiée par Redis, mais elle n’a pas été rendue au système. Pour réduire les problèmes de mémoire, vous pouvez effectuer les étapes suivantes :

   - Augmentez la taille du cache, afin de ne plus être soumis aux limitations de mémoire du système.
   - Définissez des délais d’expiration sur les clés afin que les anciennes valeurs soient supprimées de manière proactive.
   - Surveillez la mesure de cache `used_memory_rss`. Lorsque cette valeur s’approche de la taille du cache, vous risquez de voir apparaître des problèmes de performance. Répartissez les données sur plusieurs partitions si vous utilisez un cache Premium, ou augmentez la taille du cache.

   Pour plus d’informations, consultez [Saturation de la mémoire sur le serveur](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informations supplémentaires

- [Que sont les offres de cache Azure pour Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Comment puis-je évaluer et tester les performances de mon cache ?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Comment exécuter des commandes Redis ?](cache-faq.md#how-can-i-run-redis-commands)
- [Surveillance du cache Azure pour Redis](cache-how-to-monitor.md)
