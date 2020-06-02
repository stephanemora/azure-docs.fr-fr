---
title: Diagnostiquer et résoudre des problèmes lors de l’utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB
description: Utilisez des fonctionnalités telles que la journalisation côté client et d’autres outils tiers pour identifier, diagnostiquer et résoudre des problèmes liés à Azure Cosmos DB lors de l’utilisation du Kit de développement logiciel .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/06/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e389df7cfe0e228030d2d0f730fc5e671ad4c052
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927630"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostiquer et résoudre des problèmes lors de l’utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Kit SDK Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [Kit SDK Java asynchrone v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Cet article traite des problèmes courants, des solutions de contournement, des étapes de diagnostic et des outils quand vous utilisez le [Kit de développement logiciel (SDK) .NET](sql-api-sdk-dotnet.md) avec des comptes d’API SQL Azure Cosmos DB.
Le Kit de développement logiciel (SDK) .NET fournit la représentation logique côté client pour accéder à l’API SQL Azure Cosmos DB. Cet article décrit les outils et les approches qui peuvent vous aider si vous rencontrez des problèmes.

## <a name="checklist-for-troubleshooting-issues"></a>Liste de contrôle pour la résolution des problèmes
Examinez la liste de contrôle avant de mettre votre application en production. L’utilisation de la liste de contrôle empêche l’affichage de plusieurs problèmes courants. Elle permet également de diagnostiquer rapidement quand un problème se produit :

*    Utilisez le dernier [Kit de développement logiciel (SDK)](sql-api-sdk-dotnet-standard.md). Le Kit de développement logiciel (SDK) en préversion ne doit pas être utilisé en production. Cela vous évitera de rencontrer des problèmes connus déjà corrigés.
*    Consultez les [conseils relatifs aux performances](performance-tips.md) et suivez les pratiques suggérées. Cela vous aidera à éviter des problèmes de mise à l’échelle, de latence et autres liés aux performances.
*    Activez la journalisation du SDK pour vous aider à résoudre un problème. L’activation de la journalisation pouvant affecter les performances, il est préférable de ne l’activer que lors de la résolution de problèmes. Vous pouvez activer les journaux suivants :
    *    [Journaliser les métriques](monitor-accounts.md) à l’aide du portail Azure. Les métriques du portail présentent la télémétrie d’Azure Cosmos DB, qui est utile pour déterminer si le problème est lié à Azure Cosmos DB ou au client.
    *    Consignez la [chaîne de diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) dans le Kit de développement logiciel (SDK) v2 ou le [diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) dans le Kit de développement logiciel (SDK) V3 à partir des réponses d’opération ponctuelle.
    *    Journaliser les [métriques de requête SQL](sql-api-query-metrics.md) de toutes les réponses à la requête. 
    *    Suivez la configuration de la [journalisation du Kit de développement logiciel (SDK)]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md).

Jetez un coup d’œil à la section [Problèmes courants et solutions de contournement](#common-issues-workarounds) dans cet article.

Consultez la [section des problèmes GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) qui faisant l’objet d’une surveillance active. Vérifiez si un problème similaire au vôtre dispose déjà d’une solution de contournement. Si vous ne trouvez pas une solution, signalez un incident GitHub. Vous pouvez ouvrir un ticket de support pour des problèmes urgents.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problèmes courants et solutions de contournement

### <a name="general-suggestions"></a>Recommandations d’ordre général
* Exécutez autant que possible votre application dans la région Azure dans laquelle se trouve votre compte Azure Cosmos DB. 
* Vous pouvez rencontrer des problèmes de connectivité/disponibilité en raison d’un manque de ressources sur votre ordinateur client. Nous vous recommandons de surveiller votre utilisation de l’UC sur les nœuds exécutant le client Azure Cosmos DB et d’adapter leur échelle si leur charge est importante.

### <a name="check-the-portal-metrics"></a>Consulter les métriques du portail
Les [métriques du portail](monitor-accounts.md) vous aident à déterminer si un problème est lié au client ou au service. Par exemple, si les métriques contiennent un taux important de requêtes à débit limité (code d’état HTTP 429), ce qui signifie que la requête est limitée, voir la section [Taux de demandes trop élevé]. 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Délais d’expiration de demande
Une expiration de demande se produit généralement lors de l’utilisation d’une connexion directe/TCP, mais peut se produire en mode de passerelle. Ces erreurs sont les causes connues courantes et des suggestions sur la façon de résoudre le problème.

* L’utilisation élevée de l’UC entraîne une latence et/ou une expiration de délai de demande. Le client peut faire monter en puissance la machine hôte pour lui octroyer plus de ressources, ou la charge peut être répartie sur davantage de machines.
* La disponibilité de socket/port peut être faible. Quand ils s’exécutent dans Azure, les clients qui utilisent le kit SDK .NET peuvent rencontrer un problème d’épuisement de ports Azure SNAT (PAT). Pour réduire le risque de rencontrer ce problème, utilisez la dernière version 2.x ou 3.x du kit SDK .NET. Voici un exemple qui illustre la raison pour laquelle il est recommandé de toujours exécuter la dernière version du Kit de développement logiciel (SDK).
* La création de plusieurs instances de DocumentClient peut entraîner des problèmes de conflit et de dépassement de délai de connexion. Suivez les [conseils en lien avec les performances](performance-tips.md) et utilisez une seule instance DocumentClient pour l’ensemble du processus.
* Les utilisateurs sont parfois confrontés à une latence ou à des délais d’attente de requête élevés parce que leurs collections ne sont pas suffisamment approvisionnées, parce que le serveur principal limite les requêtes, ou parce que le client effectue de nouvelles tentatives en interne. Consultez les [métriques du portail](monitor-accounts.md).
* Azure Cosmos DB répartit le débit global approvisionné de manière uniforme entre les partitions physiques. Vérifiez les métriques du portail pour voir si la charge de travail rencontre une [clé de partition](partition-data.md) chaude. Cela a pour effet que le débit consommé (unités de demande/s) apparaît inférieur aux unités de demande approvisionnées, alors que le débit consommé par une seule partition dépasse le débit approvisionné. 
* De plus, le Kit de développement logiciel (SDK) 2.0 ajoute une sémantique de canal aux connexions directes/TCP. Une connexion TCP est utilisée pour traiter plusieurs demandes simultanément. Cela peut entraîner deux problèmes dans certains cas :
    * Un degré élevé de simultanéité peut entraîner des conflits sur le canal.
    * Des demandes ou réponses volumineuses peuvent entraîner un blocage en tête de ligne sur le canal et exacerber les conflits, même avec un degré relativement faible de simultanéité.
    * Si le cas s’inscrit dans l’une de ces deux catégories (ou en cas de soupçon d’utilisation élevée du processeur), les mesures d’atténuation possibles sont les suivantes :
        * Essayez d’adapter l’échelle de l’application.
        * Par ailleurs, vous pouvez capturer les journaux du Kit de développement logiciel (SDK) via l’[Écouteur de suivi](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) afin de recueillir davantage de détails.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Latence réseau élevée
Une latence réseau élevée peut être identifiée à l’aide de la [chaîne de diagnostic](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) dans le Kit de développement logiciel (SDK) V2 ou des [diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) dans le SDK V3.

Si aucun [délai d’expiration](#request-timeouts) n’est présent et que les diagnostics affichent des requêtes uniques pour lesquelles la latence élevée est évidente sur la différence entre `ResponseTime` et `RequestStartTime`, comme ceci (> à 300 millisecondes dans cet exemple) :

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Cette latence peut avoir plusieurs causes :

* Votre application ne s’exécute pas dans la même région que votre compte Azure Cosmos DB.
* La configuration de votre [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) est incorrecte et tente de se connecter à une autre région que celle dans laquelle votre application est en cours d’exécution.
* Il peut y avoir un goulot d’étranglement sur l’interface réseau en raison d’un trafic élevé. Si l’application s’exécute sur des machines virtuelles Azure, voici les solutions possibles :
    * Envisagez d’utiliser une [machine virtuelle avec la mise en réseau accélérée activée](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Activez la [mise en réseau accélérée sur une machine virtuelle existante](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Envisagez d’utiliser un [ordinateur virtuel haut de gamme](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Insuffisance de ports Azure SNAT (PAT)

Si votre application est déployée sur des [Machines virtuelles Azure sans adresse IP publique](../load-balancer/load-balancer-outbound-connections.md#defaultsnat), par défaut les [ports Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) établissent des connexions avec n’importe quel point de terminaison en dehors de votre machine virtuelle. Le nombre de connexions autorisées de la machine virtuelle au point de terminaison Azure Cosmos DB est limité par la [configuration Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Cette situation peut aboutir à la limitation de la connexion, à la fermeture de la connexion ou aux [délais d’expiration de la requête](#request-timeouts) mentionnés ci-dessus.

 Les ports Azure SNAT sont utilisés uniquement quand votre machine virtuelle a une adresse IP privée et tente de se connecter avec une adresse IP publique. Il existe deux solutions de contournement pour éviter la limitation d’Azure SNAT (à condition que vous utilisiez déjà une seule instance client sur l’ensemble de l’application) :

* Ajoutez votre point de terminaison de service Azure Cosmos DB au sous-réseau de votre réseau virtuel Machines virtuelles Azure. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quand le point de terminaison de service est activé, les requêtes ne sont plus envoyées d’une adresse IP publique à Azure Cosmos DB. Au lieu de cela, les identités du réseau virtuel et du sous-réseau sont envoyées. Cette modification peut entraîner des problèmes de pare-feu si seules les adresses IP publiques sont autorisées. Si vous utilisez un pare-feu, quand vous activez le point de terminaison de service, ajoutez un sous-réseau au pare-feu à l’aide de [Listes de contrôle d’accès de réseau virtuel](../virtual-network/virtual-networks-acl.md).
* Assignez une [adresse IP publique à votre machine virtuelle Azure](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Serveur proxy HTTP
Si vous utilisez un proxy HTTP, vérifiez qu’il peut prendre en charge le nombre de connexions configuré dans SDK `ConnectionPolicy`.
Sinon, vous serez confronté à des problèmes de connexion.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Taux de requêtes trop élevé
Le message « Taux de requêtes trop élevé » ou le code d’erreur 429 indiquent que vos requêtes sont limitées parce que le débit consommé (unités de requête/s) a dépassé le [débit approvisionné](set-throughput.md). Le Kit de développement logiciel (SDK) envoie automatiquement de nouvelles demandes en fonction de la [stratégie de nouvelles tentatives](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet) en place. Si cet échec se produit souvent, envisagez d’augmenter le débit de la collection. Examinez les [métriques du portail](use-metrics.md) pour voir si vous obtenez des erreurs 429. Examinez votre [clé de partition](partitioning-overview.md#choose-partitionkey) pour vous assurer qu’elle entraîne une répartition uniforme du volume de stockage et de demande. 

### <a name="slow-query-performance"></a>Performances des requêtes lentes
Les [métriques de requête](sql-api-query-metrics.md) vous aident à déterminer où la requête passe la majeure partie du temps. Elle vous permettent de voir quelle portion du temps est consacrée au serveur principal et au client.
* Si la requête principale retourne rapidement des résultats, et passe beaucoup de temps sur le client, vérifiez la charge de la machine. Il est probable que les ressources sont insuffisantes et que le Kit de développement logiciel (SDK) attend que des ressources soient disponibles pour gérer la réponse.
* Si la requête principale est lente, essayez de l’[optimiser](optimize-cost-queries.md) et d’examiner la [stratégie d’indexation](index-overview.md) actuelle. 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401 : la signature MAC trouvée dans la requête HTTP n’est pas la même que la signature calculée
Si vous avez reçu le message d’erreur 401 suivant : « La signature MAC trouvée dans la requête HTTP n’est pas la même que la signature calculée », cela peut être dû aux scénarios suivants.

1. La clé a été pivotée et n’a pas respecté les [meilleures pratiques](secure-access-to-data.md#key-rotation). Il s’agit du scénario le plus fréquent. La rotation des clés de compte Cosmos DB peut prendre de quelques secondes à plusieurs jours, selon la taille du compte Cosmos DB.
   1. L’erreur 401 de signature MAC est visible peu après une rotation de clé et finit par s’arrêter sans aucune modification. 
2. La clé est mal configurée sur l’application, de sorte que la clé ne correspond pas au compte.
   1. Le problème 401 de signature MAC est cohérent et se produit pour tous les appels.
3. Il existe une condition de concurrence avec la création d’un conteneur. Une instance d’application tente d’accéder au conteneur avant que la création du conteneur soit terminée. C’est le cas le plus fréquent si l’application est en cours d’exécution et que le conteneur est supprimé et recréé avec le même nom pendant que l’application est en cours d’exécution. Le Kit de développement logiciel (SDK) tentera d’utiliser le nouveau conteneur, mais la création du conteneur est toujours en cours, de sorte qu’il n’en a pas les clés.
   1. Le problème 401 de signature MAC s’affiche peu après la création d’un conteneur et ne survient que pendant le processus de création du conteneur.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>Erreur HTTP 400. La taille des en-têtes de requête est trop longue.
 La taille de l’en-tête est devenue trop grande et dépasse la taille maximale autorisée. Il est toujours recommandé d’utiliser le Kit de développement logiciel (SDK) le plus récent. Veillez à utiliser au moins la version [3.x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) ou [2.x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md), qui ajoute le suivi de la taille d’en-tête au message d’exception.

Causes :
 1. Le jeton de session est devenu trop grand. Le jeton de session croît à mesure que le nombre de partitions dans le conteneur augmente.
 2. Le jeton de continuation est devenu trop grand. Différentes requêtes auront des tailles de jeton de continuation différentes.
 3. Cela est dû à une combinaison du jeton de session et du jeton de continuation.

Solution :
   1. Suivez les [conseils sur les performances](performance-tips.md) et convertissez l’application en mode de connexion Direct + TCP. Le mode Direct + TCP n’a pas de restriction de taille d’en-tête comme le protocole HTTP, ce qui évite le problème.
   2. Si le jeton de session est la cause du problème, une atténuation temporaire consiste à redémarrer l’application. Le redémarrage de l’instance d’application entraîne la réinitialisation du jeton de session. Si les exceptions s’arrêtent après le redémarrage, cela confirme que le jeton de session est bien la cause. Il finira par atteindre à nouveau la taille à l’origine de l’exception.
   3. Si l’application ne peut pas être convertie en mode Direct + TCP et que le jeton de session est la cause du problème, une atténuation est possible en modifiant le [niveau de cohérence](consistency-levels.md) du client. Le jeton de session est utilisé uniquement pour la cohérence de session, ce qui est le réglage par défaut pour Cosmos DB. Tout autre niveau de cohérence n’utilise pas le jeton de session. 
   4. Si l’application ne peut pas être convertie en mode Direct + TCP et que le jeton de continuation est la cause du problème, essayez de définir l’option ResponseContinuationTokenLimitInKb. L’option est accessible dans FeedOptions pour v2 ou QueryRequestOptions dans v3.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taux de demandes trop élevé]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
