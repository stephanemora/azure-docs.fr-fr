---
title: Résoudre les problèmes de requêtes lentes Azure Cosmos DB avec le kit de développement logiciel .NET
description: Découvrez comment diagnostiquer et résoudre les requêtes lentes lors de l’utilisation du kit de développement logiciel (SDK) .NET Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 06/15/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 16ec94b60288215b55e0332239ba3d603b210673
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116316"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-slow-requests"></a>Diagnostiquer et résoudre les problèmes de requêtes lentes Azure Cosmos DB avec le SDK .NET
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La lenteur des requêtes Azure Cosmos DB peut avoir de multiples raisons, telles que la limitation des requêtes ou la façon dont votre application est conçue. Cet article explique les différentes causes du problème. 

## <a name="request-rate-too-large-429-throttles"></a>Taux de requêtes trop élevé (limitations 429)

La limitation des requêtes est la cause la plus courante pour les requêtes lentes. Azure Cosmos DB limite les requêtes si elles dépassent les RU allouées à la base de données ou au conteneur. Le kit de développement logiciel (SDK) intègre une logique permettant de retenter ces requêtes. L’article [Taux de requêtes trop élevé](troubleshoot-request-rate-too-large.md#how-to-investigate) de résolution des problèmes explique comment vérifier si les requêtes sont limitées et comment mettre à l’échelle votre compte pour éviter ces problèmes à l’avenir.

## <a name="application-design"></a>Conception des applications

Si votre application ne suit pas les meilleures pratiques du kit de développement logiciel (SDK), cela peut entraîner des problèmes différents qui provoqueront des requêtes lentes ou en échec. Suivez les [meilleures pratiques du SDK .NET](performance-tips-dotnet-sdk-v3-sql.md) pour améliorer les performances.

Tenez compte des éléments suivants lors du développement de votre application :
* Votre application ne s’exécute pas dans la même région que votre compte Azure Cosmos DB.
* Instance singleton de l’instance du kit de développement logiciel (SDK). Le kit de développement logiciel (SDK) comporte plusieurs caches qui doivent être initialisés, ce qui peut ralentir les toutes premières requêtes. 
* Utiliser le mode de connectivité Direct + TCP
* Évitez l’utilisation élevée du processeur. Veillez à consulter Max CPU et non Average, qui est la valeur par défaut pour la plupart des systèmes de journalisation. Toute valeur supérieure à environ 40 % peut augmenter la latence.


## <a name="capture-the-diagnostics"></a><a name="capture-diagnostics"></a>Capturer les diagnostics

Toutes les réponses du kit de développement logiciel (SDK) y compris `CosmosException` possèdent une propriété Diagnostics. Cette propriété enregistre toutes les informations relatives à la requête unique, notamment en cas de nouvelles tentatives ou d’échecs temporaires. 

Les diagnostics sont retournés sous forme de chaîne. La chaîne change avec chaque version, car elle est améliorée pour mieux résoudre les différents scénarios. Avec chaque version du SDK, le formatage de la chaîne comporte des changements cassants. N’analysez pas la chaîne pour éviter les changements cassants. L’exemple de code suivant montre comment lire les journaux de diagnostic à l’aide du kit de développement logiciel (SDK) .NET :

```c#
try
{
    ItemResponse<Book> response = await this.Container.CreateItemAsync<Book>(item: testItem);
    if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan)
    {
        // Log the diagnostics and add any additional info necessary to correlate to other logs 
        Console.Write(response.Diagnostics.ToString());
    }
}catch(CosmosException cosmosException){
    // Log the full exception including the stack trace 
    Console.Write(cosmosException.ToString());
    // The Diagnostics can be logged separately if required.
    Console.Write(cosmosException.Diagnostics.ToString());
}

ResponseMessage response = await this.Container.CreateItemStreamAsync(partitionKey, stream);
if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan || IsFailureStatusCode(response.StatusCode))
{
    // Log the diagnostics and add any additional info necessary to correlate to other logs 
    Console.Write(response.Diagnostics.ToString());
}
```


## <a name="diagnostics-in-version-319-and-higher"></a>Diagnostics dans la version 3.19 et versions ultérieures
La structure JSON présente des changements cassants à chaque version du kit de développement logiciel (SDK). Cela rend l’analyse risquée. Le fichier JSON représente une structure arborescente de la requête qui passe par le kit de développement logiciel (SDK). Voici quelques éléments clés à examiner :

### <a name="cpu-history"></a><a name="cpu-history"></a>Historique de processeur
Une utilisation élevée du processeur est la cause la plus courante de requêtes lentes. Pour une latence optimale, l'utilisation du processeur doit être d'environ 40 %. Utilisez 10 secondes comme intervalle pour superviser l'utilisation maximale (non moyenne) du processeur. Les pics d’utilisation du processeur sont plus courants avec les requêtes entre partitions où les requêtes peuvent établir plusieurs connexions pour une seule requête.

Si l’erreur contient des informations sur `TransportException`, elle peut également contenir `CPU History` :

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Si l’utilisation du processeur dépasse 70 %, le délai d’expiration est probablement provoqué par un épuisement du processeur. Dans ce cas, la solution consiste à examiner la source de l’utilisation élevée du processeur et à la réduire, ou à augmenter les ressources de la machine.
* Si les mesures du processeur ne se produisent pas toutes les 10 secondes, par exemple, les intervalles ou les temps de mesure indiquent un plus grand laps de temps entre les mesures. Dans ce cas, la cause est la pénurie de threads. La solution consiste à étudier la ou les sources de la privation de thread (threads potentiellement verrouillés) ou à augmenter les ressources de la ou des machines.

#### <a name="solution"></a>Solution :
L'application cliente qui utilise le SDK doit faire l'objet d'un scale-up ou d'un scale-out.


### <a name="httpresponsestats"></a><a name="httpResponseStats"></a>HttpResponseStats
HttpResponseStats constitue une requête dirigée vers la demande à la [passerelle](sql-sdk-connection-modes.md). Même en mode direct, le kit de développement logiciel (SDK) obtient toutes les informations de métadonnées à partir de la passerelle.

Si la requête est lente, vérifiez d’abord que toutes les suggestions ci-dessus ne produisent pas de résultats.

S’il est toujours lent, les différents modèles pointent vers différents problèmes :

Résultat de magasin unique pour une requête unique

| Nombre de demandes | Scénario | Description | 
|----------|-------------|-------------|
| Unique pour tout | Délai d’expiration de la requête ou HttpRequestExceptions | Pointe vers l’[épuisement du port SNAT](troubleshoot-dot-net-sdk.md#snat) ou un manque de ressources sur l’ordinateur pour traiter la requête à temps. |
| Pourcentage unique ou faible (le contrat de niveau de service est respecté) | Tous | Un faible pourcentage de requêtes lentes peut être causé par plusieurs problèmes transitoires différents et doit être envisagé. | 
| Tous | Tous | Pointe vers un problème lié à l’infrastructure ou à la mise en réseau. |
| Contrat SLA non respecté | Aucune modification à l’application et contrat de niveau de service annulé | Pointe vers un problème lié au service Azure Cosmos DB. |

```json
"HttpResponseStats": [
    {
        "StartTimeUTC": "2021-06-15T13:53:09.7961124Z",
        "EndTimeUTC": "2021-06-15T13:53:09.7961127Z",
        "RequestUri": "https://127.0.0.1:8081/dbs/347a8e44-a550-493e-88ee-29a19c070ecc/colls/4f72e752-fa91-455a-82c1-bf253a5a3c4e",
        "ResourceType": "Collection",
        "HttpMethod": "GET",
        "ActivityId": "e16e98ec-f2e3-430c-b9e9-7d99e58a4f72",
        "StatusCode": "OK"
    }
]
```

### <a name="storeresult"></a><a name="storeResult"></a>StoreResult
StoreResult représente une requête unique à Azure Cosmos DB à l’aide du mode Direct avec le protocole TCP.

S’il est toujours lent, les différents modèles pointent vers différents problèmes :

Résultat de magasin unique pour une requête unique

| Nombre de demandes | Scénario | Description | 
|----------|-------------|-------------|
| Unique pour tout | StoreResult contient TransportException | Pointe vers l’[épuisement du port SNAT](troubleshoot-dot-net-sdk.md#snat) ou un manque de ressources sur l’ordinateur pour traiter la requête à temps. |
| Pourcentage unique ou faible (le contrat de niveau de service est respecté) | Tous | Un faible pourcentage de requêtes lentes peut être causé par plusieurs problèmes transitoires différents et doit être envisagé. | 
| Tous | Tous | Problème lié à l’infrastructure ou à la mise en réseau. |
| Contrat SLA non respecté | Les requêtes contiennent plusieurs codes d’erreur d’échec tels que 410 et IsValid a la valeur true | Pointe vers un problème lié au service Cosmos DB |
| Contrat SLA non respecté | Les requêtes contiennent plusieurs codes d’erreur d’échec tels que le code 410 et IsValid a pour valeur false | Pointe vers un problème avec la machine |
| Contrat SLA non respecté | StorePhysicalAddress est le même sans code d’état d’échec | Problème potentiel avec le service Cosmos DB |
| Contrat SLA non respecté | StorePhysicalAddress possède le même ID de partition mais des ID de réplica différents sans code d’état d’échec | Problème potentiel avec le service Cosmos DB |
| Contrat SLA non respecté | StorePhysicalAddress est aléatoire sans code d’état d’échec | Pointe vers un problème avec la machine |

Plusieurs StoreResults pour une requête unique :

* La cohérence des valeurs maximales et des obsolescences limitées possède toujours au moins deux résultats de magasin
* Vérifiez le code d’état de chaque StoreResult. Le kit de développement logiciel (SDK) réessaie automatiquement sur plusieurs [défaillances transitoires](troubleshoot-dot-net-sdk-request-timeout.md) différentes. Le kit de développement logiciel (SDK) est constamment amélioré pour couvrir davantage de scénarios. 

### <a name="rntbdrequeststats"></a><a name="rntbdRequestStats"></a>RntbdRequestStats 
Affichez l’heure des différentes étapes de l’envoi et de la réception d’une requête dans la couche transport.

* ChannelAcquisitionStarted : heure à laquelle obtenir ou créer une nouvelle connexion. De nouvelles connexions peuvent être créées pour plusieurs régions différentes. Par exemple, une connexion a été fermée de manière inattendue ou des requêtes sont envoyées en nombre excessif via les connexions existantes afin qu’une nouvelle connexion soit créée. 
* Le temps de pipeline est considérable et peut être dû à une requête volumineuse.
* Le temps de transit est élevé, ce qui provoque un problème réseau. Comparez ce nombre à `BELatencyInMs`. Si BELatencyInMs a une valeur faible, le temps a été passé sur le réseau et non sur le service Azure Cosmos DB.
* L’heure de réception est importante, ce qui pointe vers un problème de privation de thread. Il s’agit de la durée entre la réponse et le retour du résultat.

```json
"StoreResult": {
    "ActivityId": "a3d325c1-f4e9-405b-820c-bab4d329ee4c",
    "StatusCode": "Created",
    "SubStatusCode": "Unknown",
    "LSN": 1766,
    "PartitionKeyRangeId": "0",
    "GlobalCommittedLSN": -1,
    "ItemLSN": -1,
    "UsingLocalLSN": false,
    "QuorumAckedLSN": 1765,
    "SessionToken": "-1#1766",
    "CurrentWriteQuorum": 1,
    "CurrentReplicaSetSize": 1,
    "NumberOfReadRegions": 0,
    "IsClientCpuOverloaded": false,
    "IsValid": true,
    "StorePhysicalAddress": "rntbd://127.0.0.1:10253/apps/DocDbApp/services/DocDbServer92/partitions/a4cb49a8-38c8-11e6-8106-8cdcd42c33be/replicas/1p/",
    "RequestCharge": 11.05,
    "BELatencyInMs": "7.954",
    "RntbdRequestStats": [
        {
            "EventName": "Created",
            "StartTime": "2021-06-15T13:53:10.1302477Z",
            "DurationInMicroSec": "6383"
        },
        {
            "EventName": "ChannelAcquisitionStarted",
            "StartTime": "2021-06-15T13:53:10.1366314Z",
            "DurationInMicroSec": "96511"
        },
        {
            "EventName": "Pipelined",
            "StartTime": "2021-06-15T13:53:10.2331431Z",
            "DurationInMicroSec": "50834"
        },
        {
            "EventName": "Transit Time",
            "StartTime": "2021-06-15T13:53:10.2839774Z",
            "DurationInMicroSec": "17677"
        },
        {
            "EventName": "Received",
            "StartTime": "2021-06-15T13:53:10.3016546Z",
            "DurationInMicroSec": "7079"
        },
        {
            "EventName": "Completed",
            "StartTime": "2021-06-15T13:53:10.3087338Z",
            "DurationInMicroSec": "0"
        }
    ],
    "TransportException": null
}
```

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Le taux d'échec n'est pas conforme au contrat de niveau de service Azure Cosmos DB
Contactez [Support Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) des problèmes lors de l’utilisation du kit de développement logiciel (SDK) .NET Azure Cosmos DB.
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md).
