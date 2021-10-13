---
title: Résoudre les problèmes côté client liés à Azure Cache pour Redis
description: Découvrez comment résoudre les problèmes courants côté client liés à Azure Cache pour Redis, comme les sollicitations de la mémoire du client, les pics de trafic, l’utilisation élevée du processeur, les limites de bande passante, les demandes volumineuses ou les réponses de grande taille.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: bd0ca3b20cc37ecf2107e03eea5d6e4a62633f16
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807192"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Résoudre les problèmes côté client liés à Azure Cache pour Redis

Cette section traite de la résolution des problèmes qui se produisent à la suite d’une condition sur le client Redis utilisé par votre application.

- [Sollicitation de la mémoire sur le client Redis](#memory-pressure-on-redis-client)
- [Augmentation de trafic](#traffic-burst)
- [Utilisation importante du processeur du client](#high-client-cpu-usage)
- [Limitation de la bande passante côté client](#client-side-bandwidth-limitation)
<!-- [Large request or response size](#large-request-or-response-size) -->

## <a name="memory-pressure-on-redis-client"></a>Sollicitation de la mémoire sur le client Redis

La sollicitation de la mémoire sur l’ordinateur client génère toutes sortes de problèmes de performances susceptibles de retarder le traitement des réponses provenant du cache. Lorsque la mémoire est trop sollicitée, le système peut paginer les données vers le disque. Cette _pagination_ ralentit considérablement le système.

Pour détecter une sollicitation de la mémoire sur le client :

- Supervisez l’utilisation de la mémoire sur l’ordinateur et vérifiez qu’elle ne dépasse pas la mémoire disponible.
- Supervisez le compteur de performances `Page Faults/Sec` du client. Lors d’un fonctionnement normal, la plupart des systèmes peuvent rencontrer des défauts de page. Les pics de défauts de page correspondant à des délais d’attente de requêtes peuvent indiquer une sollicitation de la mémoire.

Vous pouvez réduire la sursollicitation de la mémoire sur le client de plusieurs façons :

- Analysez vos modèles d’utilisation de la mémoire pour réduire la consommation de mémoire sur le client.
- Mettez à niveau votre machine virtuelle cliente pour bénéficier de davantage de mémoire.

## <a name="traffic-burst"></a>Augmentation de trafic

Les augmentations de trafic combinées à des paramètres `ThreadPool` insatisfaisants peuvent retarder le traitement des données déjà envoyées par le serveur Redis, mais pas encore consommées côté client.

Supervisez la façon dont évoluent vos statistiques `ThreadPool` au fil du temps à l’aide d’un [exemple `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Pour une analyse plus approfondie, vous pouvez utiliser les messages `TimeoutException` de StackExchange.Redis comme ci-dessous :

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

Dans l’exception précédente, plusieurs problèmes sont intéressants :

- Notez que dans les sections `IOCP` et `WORKER`, la valeur de `Busy` est supérieure à la valeur de `Min`. Cette différence signifie que vos paramètres `ThreadPool` ont besoin d’être ajustés.
- Vous pouvez également voir `in: 64221`. Cette valeur indique que 64 211 octets ont été reçus au niveau de la couche de socket du noyau du client, mais qu’ils n’ont pas encore été lus par l’application. En général, cette différence signifie que votre application (par exemple, StackExchange.Redis) ne lit pas les données du réseau aussi rapidement que le serveur les lui envoie.

Vous pouvez [configurer vos paramètres `ThreadPool`](cache-management-faq.yml#important-details-about-threadpool-growth) pour que votre pool de threads puisse rapidement faire l’objet d’un scale-up en cas d’augmentation du trafic.

## <a name="high-client-cpu-usage"></a>Utilisation importante du processeur du client

Une utilisation élevée du processeur du client indique que le système ne peut pas exécuter la charge de travail qui lui a été ajoutée dans un délai normal. Même si le cache a envoyé la réponse rapidement, le client ne peut pas traiter la réponse en temps voulu.

Supervisez l’utilisation du processeur du client à l’échelle du système à l’aide des métriques qui sont disponibles dans le portail Azure ou via les compteurs de performances de l’ordinateur. Ne tenez pas compte du processeur de *processus*, car le processeur peut très bien être faiblement utilisé par un processus tout en étant fortement sollicité à l’échelle du système. Regardez les pics d’utilisation du processeur qui correspondent à des délais d’expiration. Quand le processeur est très utilisé, vous pouvez également voir s’afficher des valeurs `in: XXX` élevées dans les messages d’erreur `TimeoutException`, comme ceux décrits dans la section [Augmentation de trafic](#traffic-burst).

> [!NOTE]
> StackExchange.Redis 1.1.603 et versions ultérieures inclut la mesure `local-cpu` dans les messages d’erreur `TimeoutException`. Vérifiez que vous utilisez la dernière version en date du [package NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Nous corrigeons en permanence le code pour le rendre plus robuste aux délais d’expiration. Il est donc primordial d’utiliser la dernière version.
>

Pour remédier à une utilisation élevée du processeur d’un client :

- Recherchez la cause des pics d’utilisation du processeur.
- Mettez à niveau votre machine virtuelle cliente pour bénéficier d’une plus grande capacité de processeur.

## <a name="client-side-bandwidth-limitation"></a>Limitation de la bande passante côté client

Selon l’architecture des ordinateurs clients, ces ordinateurs peuvent afficher des limites concernant la bande passante réseau qu’ils peuvent utiliser. Si le client dépasse la bande passante disponible en surchargeant la capacité du réseau, les données ne sont pas traitées côté client aussi rapidement que le serveur les envoie. Cette situation peut entraîner des délais d’attente.

Supervisez la façon dont votre utilisation de la bande passante évolue au fil du temps, à l’aide d’un [exemple `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ce code peut ne pas s’exécuter correctement dans certains environnements aux autorisations restreintes (tels que les sites web Azure).

Réduisez l’utilisation de la bande passante réseau ou augmentez la taille de la machine virtuelle cliente pour bénéficier d’une plus grande capacité réseau.

<!-- 
## Large request or response Size

A large request/response can cause timeouts. As an example, suppose your timeout value configured on your client is 1 second. Your application requests two keys (for example, 'A' and 'B') at the same time (using the same physical network connection). Most clients support request "pipelining", where both requests 'A' and 'B' are sent one after the other without waiting for their responses. The server sends the responses back in the same order. If response 'A' is large, it can eat up most of the timeout for later requests.

In the following example, request 'A' and 'B' are sent quickly to the server. The server starts sending responses 'A' and 'B' quickly. Because of data transfer times, response 'B' must wait behind response 'A' times out even though the server responded quickly.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

This request/response is a difficult one to measure. You could instrument your client code to track large requests and responses.

Resolutions for large response sizes are varied but include:

1. Optimize your application for a large number of small values, rather than a few large values.
    - The preferred solution is to break up your data into related smaller values.
    - See the post [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) for details on why smaller values are recommended.
1. Increase the size of your VM to get higher bandwidth capabilities
    - More bandwidth on your client or server VM may reduce data transfer times for larger responses.
    - Compare your current network usage on both machines to the limits of your current VM size. More bandwidth on only the server or only on the client may not be enough.
1. Increase the number of connection objects your application uses.
    - Use a round-robin approach to make requests over different connection objects.

 -->
## <a name="additional-information"></a>Informations supplémentaires

- [Résoudre les problèmes côté serveur liés à Azure Cache pour Redis](cache-troubleshoot-server.md)
- [Comment puis-je évaluer et tester les performances de mon cache ?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
