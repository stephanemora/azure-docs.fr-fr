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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830005"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Résolution des problèmes du cache Azure pour Redis

Cet article vous aide à résoudre les problèmes de différentes catégories de problèmes que vous pouvez rencontrer lors de la connexion avec le Cache Azure pour les instances de Redis.

- [Résolution des problèmes côté client](#client-side-troubleshooting) permet d’identifier et résoudre les problèmes dans l’application qui se connecte à votre cache.
- [Résolution des problèmes côté serveur](#server-side-troubleshooting) permet d’identifier et résoudre les problèmes qui se produisent sur le Cache Azure Redis côté.
- [Résolution des problèmes de perte de données](#data-loss-troubleshooting) permet d’identifier et résoudre les incidents où les clés sont attendus, mais introuvable dans le cache.
- [Exceptions de délai d’expiration de StackExchange.Redis](#stackexchangeredis-timeout-exceptions) fournit des instructions spécifiques sur la résolution des problèmes avec la bibliothèque de StackExchange.Redis.

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

Sollicitation de la mémoire sur l’ordinateur client génère toutes sortes de problèmes de performances qui peuvent retarder le traitement des réponses à partir du cache. Saturation de mémoire, le système peut données de page sur le disque. Cette _pagination_ ralentit considérablement le système.

Pour détecter la pression de mémoire sur le client :

- Surveillez l’utilisation de mémoire sur l’ordinateur pour vous assurer qu’il ne dépasse pas la mémoire disponible.
- Surveiller le client `Page Faults/Sec` compteur de performances. En fonctionnement normal, la plupart des systèmes rencontrent des. Pics de défauts de page correspondant à la demande des délais d’attente peuvent indiquer une sollicitation de la mémoire.

Pression de mémoire élevée sur le client peut être atténuée de plusieurs façons :

- Explorez vos modèles d’utilisation de mémoire pour réduire la consommation de mémoire sur le client.
- Mettre à niveau votre machine virtuelle cliente pour une plus grande taille avec davantage de mémoire.

### <a name="burst-of-traffic"></a>Augmentation de trafic

Les augmentations de trafic combinées à des paramètres `ThreadPool` insatisfaisants peuvent retarder le traitement des données déjà envoyées par le serveur Redis, mais pas encore consommées côté client.

Moniteur comment votre `ThreadPool` statistiques changer au fil du temps à l’aide [exemple `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Vous pouvez utiliser `TimeoutException` messages à partir de StackExchange.Redis comme ci-dessous examiner en détail :

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Dans l’exception précédente, il existe plusieurs problèmes sont intéressants :

- Notez que dans les sections `IOCP` et `WORKER`, la valeur de `Busy` est supérieure à la valeur de `Min`. Cette différence signifie que votre `ThreadPool` paramètres doivent ajuster.
- Vous pouvez également voir `in: 64221`. Cette valeur indique que 64,211 octets ont été reçus au niveau de la couche de socket du noyau du client, mais n’ont pas été lues par l’application. En général, cette différence signifie que votre application (par exemple StackExchange.Redis) ne lit pas les données à partir du réseau aussi rapidement que le serveur est lui envoie.

Vous pouvez [configurer votre `ThreadPool` paramètres](https://gist.github.com/JonCole/e65411214030f0d823cb) pour vous assurer que votre pool de threads s’adaptent rapidement burst scénarios.

### <a name="high-client-cpu-usage"></a>Utilisation importante du processeur du client

Client élevé l’utilisation du processeur indique que le système ne peut pas suivre le travail qu’il a demandé de faire. Même si le cache a envoyé la réponse rapidement, le client ne peut pas traiter la réponse en temps voulu.

Surveiller l’utilisation de l’UC de l’échelle du système du client à l’aide de mesures disponibles dans le portail Azure ou via des compteurs de performances sur l’ordinateur. Veillez à ne pas surveiller *processus* UC, car un processus unique peut avoir une faible utilisation du processeur, mais le processeur de l’échelle du système peut être élevé. Regardez les pics d’utilisation du processeur qui correspondent à des délais d’expiration. Processeur élevé peut également provoquer de hautes `in: XXX` valeurs dans `TimeoutException` messages d’erreur comme décrit dans la [augmentation de trafic](#burst-of-traffic) section.

> [!NOTE]
> StackExchange.Redis 1.1.603 et versions ultérieures inclut la mesure `local-cpu` dans les messages d’erreur `TimeoutException`. Vérifiez que vous utilisez la dernière version en date du [package NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Nous corrigeons en permanence le code pour le rendre plus robuste aux délais d’expiration. Il est donc primordial d’utiliser la dernière version.
>
>

Pour limiter l’utilisation d’un client élevée du processeur :

- Recherchez la cause des pics du processeur.
- Mettre à niveau votre client vers une plus grande taille de machine virtuelle avec davantage de capacités processeur.

### <a name="client-side-bandwidth-exceeded"></a>Bande passante dépassée côté client

Selon l’architecture des ordinateurs clients, ces ordinateurs peuvent afficher des limites concernant la bande passante réseau qu’ils peuvent utiliser. Si le client dépasse la bande passante disponible en surchargeant la capacité du réseau, les données ne sont pas traitées côté client aussi rapidement que le serveur envoie. Cette situation peut entraîner des délais d’attente.

Surveiller l’évolution de votre bande passante au fil du temps à l’aide [exemple `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ce code peut ne pas s’exécuter correctement dans certains environnements aux autorisations restreintes (tels que les sites web Azure).

Pour atténuer, réduire la consommation de bande passante réseau ou augmenter la taille de machine virtuelle vers un autre avec davantage de capacité réseau du client.

### <a name="large-requestresponse-size"></a>Taille importante de la demande/réponse

Une demande/réponse volumineuse peut entraîner des délais d’expiration. Par exemple, supposons que votre valeur de délai d’expiration configurée sur votre client est de 1 seconde. Votre application demande deux clés (par exemple, « A » et « B ») en même temps (à l’aide de la même connexion réseau physique). La plupart des clients prennent en charge le demande « traitement en pipeline », où les deux demandes « A » et « B » sont envoyées les unes après les autres sans attendre leurs réponses. Le serveur renvoie les réponses dans le même ordre. Si la réponse « A » est volumineuse, elle peut saturer la majeure partie du délai d’expiration pour les demandes ultérieures.

Dans l’exemple suivant, la demande « A » et « B » sont envoyées rapidement au serveur. Le serveur commence à envoyer rapidement des réponses « A » et « B ». En raison des temps de transfert de données, « B » doit attendre derrière la réponse « A » de réponse arrive à expiration même si le serveur a répondu rapidement.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Cette demande/réponse est difficile à mesurer. Vous pouvez instrumenter votre code client pour effectuer le suivi des requêtes et réponses volumineuses.

Solutions possibles pour les tailles de réponse volumineuse sont variés mais incluent :

1. Optimiser votre application pour un grand nombre de petites valeurs plutôt que quelques grandes valeurs.
    - La meilleure solution consiste à diviser vos données en valeurs plus petites.
    - Consultez le billet [quelle est la plage de tailles de valeur idéale pour redis ? 100 Ko est trop volumineux ? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) pour plus d’informations sur les raisons des valeurs moins élevées sont recommandées.
1. Augmenter la taille de votre machine virtuelle pour obtenir des capacités de bande passante plus élevées
    - Davantage de bande passante sur votre client ou d’une machine virtuelle du serveur peut réduire le temps de transfert de données des réponses volumineuses.
    - Comparez l’utilisation actuelle du réseau sur les deux ordinateurs pour les limites de taille de votre machine virtuelle en cours. Davantage de bande passante uniquement sur le serveur ou uniquement sur le client ne peut pas être suffisant.
1. Augmenter le nombre d’objets de connexion qu'utilise votre application.
    - Utiliser une approche de tourniquet (round-robin) pour envoyer des requêtes sur les objets de connexion différents.

## <a name="server-side-troubleshooting"></a>Résolution des problèmes côté serveur

Cette section traite des problèmes qui surviennent en raison d’un souci sur le serveur du cache.

- [Saturation de la mémoire sur le serveur](#memory-pressure-on-the-server)
- [Utilisation élevée du processeur / charge importante du serveur](#high-cpu-usage--server-load)
- [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Saturation de la mémoire sur le serveur

La saturation de la mémoire sur le serveur génère toutes sortes de problèmes de performance susceptibles de retarder le traitement des demandes. Saturation de mémoire, le système peut données de page sur le disque. Cette _pagination_ ralentit considérablement le système. Plusieurs causes peuvent être à l’origine de cette saturation de la mémoire :

- Le cache est rempli avec des données près de sa capacité maximale.
- Redis constate la fragmentation de mémoire haute. Cette fragmentation est plus souvent provoquée par le stockage d’objets volumineux dans la mesure où Redis est optimisé pour les petits objets.

Redis fournit deux statistiques via la [INFO](https://redis.io/commands/info) commande qui peut vous aider à identifier ce problème : « used_memory » et « used_memory_rss ». Vous pouvez [afficher ces mesures](cache-how-to-monitor.md#view-metrics-with-azure-monitor) à l’aide du portail.

Il existe plusieurs modifications possibles, que vous pouvez apporter pour aider à garder l’utilisation de la mémoire :

- [Configurez une stratégie de mémoire](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) et définissez des délais d’expiration sur vos clés. Cette stratégie peut ne pas suffire si vous constatez une fragmentation.
- [Configurez une valeur maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) suffisamment grande pour compenser la fragmentation de la mémoire. Pour plus d’informations, consultez supplémentaires [considérations pour les réservations de mémoire](#considerations-for-memory-reservations) ci-dessous.
- Divisez vos objets volumineux mis en cache en objets plus petits.
- [Créer des alertes](cache-how-to-monitor.md#alerts) sur des métriques telles que la mémoire utilisée pour être averti dès le début des impacts potentiels.
- [Mise à l’échelle](cache-how-to-scale.md) pour une plus grande taille de cache avec davantage de capacités de mémoire.

#### <a name="considerations-for-memory-reservations"></a>Considérations pour les réservations de mémoire

Mettre à jour les valeurs de réservation de mémoire, telles que maxmemory-reserved, peut affecter les performances du cache. Supposons que vous avez un cache de 53 Go est rempli avec 49 Go de données. Modification de la valeur de réservation à 8 Go supprime la mémoire maximale disponible du système à 45 Go. Si _used_memory_ ou _used_memory_rss_ valeurs sont supérieures à 45 Go, le système peut supprimer des données jusqu'à ce que les deux _used_memory_ et _used_memory_rss_ sont inférieures à 45 Go. La suppression de données peut augmenter la fragmentation de la charge et de la mémoire du serveur.

### <a name="high-cpu-usage--server-load"></a>Utilisation élevée du processeur / charge importante du serveur

Une charge élevée de serveur ou l’utilisation du processeur signifie que le serveur ne peut pas traiter les demandes en temps voulu. Le serveur peut être lente et ne peut pas faire face au taux de demandes.

[Surveiller les mesures](cache-how-to-monitor.md#view-metrics-with-azure-monitor) telles que de la charge du processeur ou de serveur. Regardez les pics d’utilisation du processeur qui correspondent à des délais d’expiration.

Il existe plusieurs modifications, que vous pouvez apporter à réduire la charge du serveur haute :

- Recherchez la cause des pics du processeur telles que l’exécution [commandes coûteux](#expensive-commands) ou page défaillant en raison de la pression de mémoire élevée.
- [Créer des alertes](cache-how-to-monitor.md#alerts) sur des métriques telles que la charge du processeur ou de serveur pour être averti dès le début des impacts potentiels.
- [Mise à l’échelle](cache-how-to-scale.md) pour une plus grande taille de cache avec davantage de capacités processeur.

#### <a name="expensive-commands"></a>Commandes coûteux

Pas toutes les commandes Redis sont également créés : certaines sont plus coûteuses à exécuter que d’autres. Le [documentation des commandes Redis](https://redis.io/commands) montre la complexité temporelle de chaque commande. Il est recommandé de que vous passer en revue les commandes que vous exécutez sur votre cache pour comprendre l’impact sur les performances de ces commandes. Par exemple, le [clés](https://redis.io/commands/keys) commande est souvent utilisée sans savoir qu’il est une opération o (n). Vous pouvez éviter les clés à l’aide de [analyse](https://redis.io/commands/scan) afin de réduire les UC pics.

À l’aide de la [SLOWLOG](https://redis.io/commands/slowlog) commande, vous pouvez mesurer les commandes coûteux en cours d’exécution sur le serveur.

### <a name="server-side-bandwidth-exceeded"></a>Bande passante dépassée côté serveur

Tailles de cache différents ont des capacités de bande passante réseau différents. Si le serveur dépasse la bande passante disponible, les données ne sont pas envoyées au client aussi rapidement. Demandes de clients risque d’expirer, car le serveur ne peut pas transmettre des données au client assez rapidement.

Les mesures de « Cache de lecture » et « Cache d’écriture » peuvent être utilisées pour vérifier la bande passante côté serveur est utilisée. Vous pouvez [afficher ces mesures](cache-how-to-monitor.md#view-metrics-with-azure-monitor) dans le portail.

Pour atténuer les situations où l’utilisation de la bande passante réseau est proche de capacité maximale :

- Modifier le comportement d’appel client afin de réduire à la demande du réseau.
- [Créer des alertes](cache-how-to-monitor.md#alerts) métriques telles que la lecture du cache ou cache d’écriture pour être averti dès le début des impacts potentiels.
- [Mise à l’échelle](cache-how-to-scale.md) pour une plus grande taille de cache avec davantage de capacités de bande passante réseau.

## <a name="data-loss-troubleshooting"></a>Résolution des problèmes de perte de données

Je m’attendais à trouver certaines données dans mon instance du cache Azure pour Redis, mais elles semblent absentes.

Consultez la page [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) pour identifier les causes et solutions possibles.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceptions au délai d’expiration de StackExchange.Redis

StackExchange.Redis utilise une paramètre de configuration appelé `synctimeout` pour les opérations synchrones avec une valeur par défaut de 1 000 ms. Si un appel synchrone ne se termine pendant ce temps, le client StackExchange.Redis génère une erreur de délai d’expiration similaire à l’exemple suivant :

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Ce message d’erreur contient des mesures qui peuvent vous aider à identifier la cause du problème et à en trouver la solution. Le tableau suivant contient des détails sur les mesures des messages d’erreur.

| Mesure de message d’erreur | Détails |
| --- | --- |
| inst |Au cours du dernier intervalle de temps : 0 commandes ont été émises |
| mgr |Le Gestionnaire de socket fait `socket.select`, ce qui signifie qu’il demande le système d’exploitation pour désigner un socket a quelque chose à faire. Le lecteur ne lit pas activement à partir du réseau, car il pense il n’a rien à faire |
| file d'attente |73 opérations sont en cours. |
| qu |6 des opérations en cours d’exécution se trouvent dans la file d’attente en attente et n’avez pas encore été écrits au réseau sortant. |
| qs |67 des opérations en cours d’exécution ont été envoyées au serveur, mais une réponse n’est pas encore disponible. La réponse peut être `Not yet sent by the server` ou `sent by the server but not yet processed by the client.`. |
| qc |0 ou les opérations en cours d’exécution a reçu de réponse, mais ne l’avez pas encore été marquée comme terminée, car il attend la fin de la boucle d’exécution |
| wr |Il existe un enregistreur active (ce qui signifie que les 6 demandes ne sont pas ignorées) octets/enregistreurs actifs |
| commencer |Aucun lecteur n’est actif et aucun octet n’est disponible en lecture sur la carte réseau octets/lecteurs actifs |

### <a name="steps-to-investigate"></a>Procédure d’analyse

1. Comme meilleure pratique, assurez-vous que vous utilisez le modèle suivant pour se connecter en utilisant le client StackExchange.Redis.

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

1. Assurez-vous que votre serveur et l’application cliente sont dans la même région dans Azure. Par exemple, vous pouvez rencontrer des délais d’expiration lorsque votre cache est en cours de l’est des États-Unis, mais le client se trouve dans l’ouest des États-Unis et la demande ne termine pas dans le `synctimeout` intervalle ou vous pouvez également rencontrer des délais d’attente lorsque vous déboguez à partir de votre ordinateur de développement local. 

    Il est vivement recommandé de placer le cache et le client dans la même région Azure. Si votre implémentation nécessite des appels interrégionaux, vous devez régler l’intervalle `synctimeout` sur une valeur supérieure à la valeur par défaut (1000 ms), en incluant une propriété `synctimeout` dans la chaîne de connexion. L’exemple suivant montre un extrait de code d’une chaîne de connexion pour StackExchange.Redis fournies par le Cache Azure pour Redis avec un `synctimeout` de 2 000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Vérifiez que vous utilisez la dernière version en date du [package NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Nous corrigeons en permanence le code pour le rendre plus robuste aux délais d’expiration. Il est donc primordial d’utiliser la dernière version.
1. Si vos demandes sont liés par les limitations de bande passante sur le serveur ou le client, il est plus long pour qu’ils puissent effectuer et peut entraîner des délais d’attente. Pour voir si le délai d’expiration est en raison de la bande passante réseau sur le serveur, consultez [bande passante dépassée côté serveur](#server-side-bandwidth-exceeded). Pour voir si le délai d’expiration est en raison de la bande passante du réseau client, consultez [bande passante dépassée côté Client](#client-side-bandwidth-exceeded).
1. Constatez-vous une utilisation intensive du processeur sur le serveur ou sur le client ?

   - Vérifiez si vous êtes bien utilisation intensive du processeur sur votre client. Processeur élevé peut entraîner la demande ne pas d’être traitées au sein du `synctimeout` intervalle et cause une demande de délai d’attente. Augmentez la taille de votre client ou répartissez la charge pour résoudre ce problème.
   - Vérifiez si vous obtenez des UC lié sur le serveur en surveillant le `CPU` [mesure de performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Requêtes entrantes pendant que Redis est liée à l’UC peuvent entraîner ces demandes expiration du délai. Pour résoudre ce problème, vous pouvez répartir la charge sur plusieurs partitions dans un cache premium, ou effectuer une mise à niveau vers une taille ou un niveau de tarification supérieur. Pour plus d’informations, consultez [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded).
1. Le traitement de certaines commandes est-il trop long sur le serveur ? Les commandes longues qui prennent beaucoup de temps à traiter sur le serveur redis peuvent provoquer des délais d’attente. Pour plus d’informations sur les commandes longues, consultez [commandes coûteux](#expensive-commands). Vous pouvez vous connecter à votre Cache Azure pour instance Redis en utilisant le client redis-cli ou le [Console Redis](cache-configure.md#redis-console). Ensuite, exécutez le [SLOWLOG](https://redis.io/commands/slowlog) demande de commande pour voir s’il existe plus lentement que prévu. Le serveur Redis et StackExchange.Redis sont optimisés pour traiter un grand nombre de petites requêtes plutôt qu’un nombre réduit de demandes volumineuses. Fractionner vos données en segments plus petits peut améliorer les choses.

    Pour plus d’informations sur la connexion au point de terminaison SSL de votre cache à l’aide de redis-cli et stunnel, consultez le blog [Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Une charge élevée du serveur Redis peut provoquer des délais d’expiration. Vous pouvez surveiller la charge du serveur à l’aide de la `Redis Server Load` [mesure des performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Une charge serveur de 100 (valeur maximale) signifie que le serveur redis a été occupé à plein temps à traiter des demandes. Pour voir si certaines demandes monopolisent les capacités du serveur, exécutez la commande SlowLog comme indiqué dans le paragraphe précédent. Pour plus d’informations, consultez Utilisation élevée du processeur / charge importante du serveur.
1. Un autre événement côté client a-t-il pu causer un blocage du réseau ? Les événements courants incluent : mise à l’échelle le nombre d’instances de client ou le déploiement vers le bas, une nouvelle version du client, ou à l’échelle automatique est activée. Dans nos tests, nous avons découvert que mise à l’échelle ou la mise à l’échelle vers le haut/bas peut entraîner de connectivité réseau sortante à être perdues pendant plusieurs secondes. Le code de StackExchange.Redis résiste à de tels événements et rétablit la connexion. Lors de la reconnexion, toutes les demandes dans la file d’attente délai d’attente.
1. Y a-t-il eu une demande importante précédant plusieurs petites demandes au cache ayant expiré ? Le paramètre `qs` dans l’erreur message vous indique combien de requêtes ont été envoyés à partir du client au serveur, mais n’avez pas traité une réponse. Cette valeur peut continuer d’augmenter car StackExchange.Redis utilise une seule connexion TCP et ne peut lire qu’une réponse à la fois. Même si la première opération a expiré, il ne s’arrête pas plus de données d’être envoyé à ou à partir du serveur. Autres demandes sont bloquées jusqu'à ce que la demande volumineuse est terminée et peut entraîner des délais d’expiration. Une solution pour réduire le risque de délais d’expiration consiste à vérifier que le cache est assez grand pour votre charge de travail et à fractionner les valeurs importantes en valeurs plus petites. Une autre solution consiste à utiliser un pool d’objets `ConnectionMultiplexer` dans votre client et à choisir le `ConnectionMultiplexer` le moins chargé lors de l’envoi d’une nouvelle demande. Le chargement de plusieurs objets de connexion doit empêcher un délai de provoquer des autres demandes envoyées à l’expiration du délai également.
1. Si vous utilisez `RedisSessionStateProvider`, vous avez définie correctement le délai d’attente de nouvelle tentative. `retryTimeoutInMilliseconds` doit être supérieur à `operationTimeoutInMilliseconds`, sans quoi aucune nouvelle tentative ne se produit. Dans l’exemple suivant, `retryTimeoutInMilliseconds` est réglé sur 3 000. Pour plus d’informations, consultez la page [Fournisseur d’état de session ASP.NET pour cache Azure pour Redis](cache-aspnet-session-state-provider.md) et [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Comment utiliser les paramètres de configuration du fournisseur d’état de session et du fournisseur de caches de sortie).

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

1. Vérifiez l’utilisation de la mémoire sur le serveur du cache Azure pour Redis en [surveillant](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` et `Used Memory`. Si une stratégie d’éviction est en place, Redis commence la suppression de clés lorsque `Used_Memory` atteint la taille du cache. Dans l’idéal, `Used Memory RSS` doit être légèrement supérieur à `Used memory`. Une grande différence signifie que la fragmentation de mémoire (interne ou externe). Lorsque `Used Memory RSS` est inférieur à `Used Memory`, cela signifie que le système d’exploitation a permuté une partie de la mémoire cache. Si cette permutation se produit, vous pouvez rencontrer des latences importantes. Étant donné que Redis ne contrôlez comment ses allocations sont mappées aux pages de mémoire haute `Used Memory RSS` est souvent le résultat d’un pic d’utilisation de la mémoire. Lorsque le serveur Redis libère de la mémoire, l’allocateur prend la mémoire, mais il est peut-être ou ne peut-être pas, rendez la mémoire au système. Il peut y avoir une différence entre la valeur de `Used Memory` et la consommation de mémoire telle qu’elle est indiquée par le système d’exploitation. Mémoire ont été utilisée et publiée par Redis, mais pas restituée au système. Pour aider à atténuer les problèmes de mémoire, vous pouvez effectuer les étapes suivantes :

   - Mettre à niveau le cache pour une plus grande taille afin que vous n’êtes pas en cours d’exécution par rapport à des limitations de mémoire sur le système.
   - Définissez des délais d’expiration sur les clés afin que les anciennes valeurs soient supprimées de manière proactive.
   - Surveillez la mesure de cache `used_memory_rss`. Lorsque cette valeur est proche de la taille de leur cache, vous risquez de commencer à voir les problèmes de performances. Répartir les données entre plusieurs partitions si vous utilisez un cache premium, ou mettre à niveau vers une plus grande taille de cache.

   Pour plus d’informations, consultez [Saturation de la mémoire sur le serveur](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informations supplémentaires

- [Que sont les offres de cache Azure pour Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Comment puis-je évaluer et tester les performances de mon cache ?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Comment exécuter des commandes Redis ?](cache-faq.md#how-can-i-run-redis-commands)
- [Surveillance du cache Azure pour Redis](cache-how-to-monitor.md)
