---
title: Diagnostiquer et résoudre des problèmes lors de l’utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB
description: Utilisez des fonctionnalités telles que la journalisation côté client et d’autres outils tiers pour identifier, diagnostiquer et résoudre des problèmes liés à Azure Cosmos DB lors de l’utilisation du Kit de développement logiciel .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 03/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 26c16583dbbbc49d2f1be51d0cbf1a6e26e70299
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116250"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostiquer et résoudre des problèmes lors de l’utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Kit SDK Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [Kit SDK Java asynchrone v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Cet article traite des problèmes courants, des solutions de contournement, des étapes de diagnostic et des outils quand vous utilisez le [Kit de développement logiciel (SDK) .NET](sql-api-sdk-dotnet.md) avec des comptes d’API SQL Azure Cosmos DB.
Le Kit de développement logiciel (SDK) .NET fournit la représentation logique côté client pour accéder à l’API SQL Azure Cosmos DB. Cet article décrit les outils et les approches qui peuvent vous aider si vous rencontrez des problèmes.

## <a name="checklist-for-troubleshooting-issues"></a>Liste de contrôle pour la résolution des problèmes

Examinez la liste de contrôle avant de mettre votre application en production. L’utilisation de la liste de contrôle empêche l’affichage de plusieurs problèmes courants. Elle permet également de diagnostiquer rapidement quand un problème se produit :

*    Utilisez le dernier [Kit de développement logiciel (SDK)](sql-api-sdk-dotnet-standard.md). Le Kit de développement logiciel (SDK) en préversion ne doit pas être utilisé en production. Cela vous évitera de rencontrer des problèmes connus déjà corrigés.
*    Consultez les [conseils relatifs aux performances](performance-tips.md) et suivez les pratiques suggérées. Cela vous aidera à éviter des problèmes de mise à l’échelle, de latence et autres liés aux performances.
*    Activez la journalisation du SDK pour vous aider à résoudre un problème. L’activation de la journalisation pouvant affecter les performances, il est préférable de ne l’activer que lors de la résolution de problèmes. Vous pouvez activer les journaux suivants :
*    [Journaliser les métriques](../monitor-cosmos-db.md) à l’aide du portail Azure. Les métriques du portail présentent la télémétrie d’Azure Cosmos DB, qui est utile pour déterminer si le problème est lié à Azure Cosmos DB ou au client.
*    Consignez la [chaîne de diagnostics](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) dans le Kit de développement logiciel (SDK) v2 ou le [diagnostics](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) dans le Kit de développement logiciel (SDK) V3 à partir des réponses d’opération ponctuelle.
*    Journaliser les [métriques de requête SQL](sql-api-query-metrics.md) de toutes les réponses à la requête. 
*    Suivez la configuration de la [journalisation du Kit de développement logiciel (SDK)]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md).

Jetez un coup d’œil à la section [Problèmes courants et solutions de contournement](#common-issues-workarounds) dans cet article.

Consultez la [section des problèmes GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) qui faisant l’objet d’une surveillance active. Vérifiez si un problème similaire au vôtre dispose déjà d’une solution de contournement. Si vous ne trouvez pas une solution, signalez un incident GitHub. Vous pouvez ouvrir un ticket de support pour des problèmes urgents.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problèmes courants et solutions de contournement

### <a name="general-suggestions"></a>Recommandations d’ordre général
* Exécutez autant que possible votre application dans la région Azure dans laquelle se trouve votre compte Azure Cosmos DB. 
* Vous pouvez rencontrer des problèmes de connectivité/disponibilité en raison d’un manque de ressources sur votre ordinateur client. Nous vous recommandons de surveiller votre utilisation de l’UC sur les nœuds exécutant le client Azure Cosmos DB et d’adapter leur échelle si leur charge est importante.

### <a name="check-the-portal-metrics"></a>Consulter les métriques du portail
Les [métriques du portail](../monitor-cosmos-db.md) vous aident à déterminer si un problème est lié au client ou au service. Par exemple, si les métriques contiennent un taux important de requêtes à débit limité (code d’état HTTP 429), ce qui signifie que la requête est limitée, voir la section [Taux de requêtes trop élevé](troubleshoot-request-rate-too-large.md). 

## <a name="retry-logic"></a>Logique de nouvelle tentative<a id="retry-logics"></a>
Le kit de développement logiciel (SDK) Cosmos DB, en cas d’échec d’e/s, retente l’opération en cas de nouvelle tentative dans le SDK. Une nouvelle tentative de mise en place d’un échec est une bonne pratique, mais la gestion et la nouvelle tentative d’écriture échouent. Il est recommandé d’utiliser le dernier kit de développement logiciel (SDK), car la logique de nouvelle tentative est continuellement améliorée.

1. Les échecs d’e/s de lecture et d’interrogation sont retentés par le kit de développement logiciel sans les exposer à l’utilisateur final.
2. Les écritures (créer, upsert, remplacer, supprimer) ne sont pas des idempotent et, par conséquent, le kit de développement logiciel (SDK) ne peut pas toujours retenter les opérations d’écriture ayant échoué. Il est nécessaire que la logique de l’application de l’utilisateur gère l’échec et lance une nouvelle tentative.
3. [Résolution des problèmes de disponibilité du SDK](troubleshoot-sdk-availability.md) explique les nouvelles tentatives pour les comptes de Cosmos DB à plusieurs régions.

### <a name="retry-design"></a>Conception de nouvelle tentative

L’application doit être conçue pour procéder à de nouvelles tentatives en cas d’exceptions, sauf s’il s’agit d’un problème connu où les nouvelles tentatives ne sont pas utiles. Par exemple, l’application doit effectuer une nouvelle tentative sur les délais d’expiration de demande 408, car ces derniers peuvent être temporaires et une nouvelle tentative peut donc aboutir. L’application ne doit pas effectuer de nouvelle tentative sur les exceptions 400 qui signalent généralement un problème avec la demande qui doit d’abord être résolu. Une nouvelle tentative sur l’exception 400 ne corrigera pas le problème et échouera de la même manière. Le tableau ci-dessous indique les échecs connus et ceux pouvant être suivis d’une nouvelle tentative.

## <a name="common-error-status-codes"></a><a id="error-codes"></a> Codes d’état d’erreur courants

| Code d’état | Renouvelable | Description | 
|----------|-------------|-------------|
| 400 | Non | Requête incorrecte (par ex., JSON non valide, en-têtes incorrects, clé de partition incorrecte dans l’en-tête)| 
| 401 | Non | [Non autorisé](troubleshoot-unauthorized.md) | 
| 403 | Non | [Interdit](troubleshoot-forbidden.md) |
| 404 | Non | [Ressource introuvable](troubleshoot-not-found.md) |
| 408 | Oui | [La demande a expiré](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Non | Une défaillance de conflit se produit quand L’ID fourni pour une ressource sur une opération PUT ou POST a été pris par une ressource existante. Pour résoudre ce problème, utilisez un autre ID pour la ressource, car l’ID doit être unique dans tous les documents ayant la même valeur de clé de partition. |
| 410 | Oui | Exceptions disparues (échec temporaire qui ne devrait pas violer le SLA) |
| 412 | Non | Un échec de condition préalable se produit quand l’opération a spécifié un eTag différent de la version disponible sur le serveur. Il s’agit d’une erreur d’accès concurrentiel optimiste. Effectuez une nouvelle tentative de requête après avoir lu la dernière version de la ressource et mis à jour l’eTag sur la requête.
| 413 | Non | [Entité de requête trop grande](../concepts-limits.md#per-item-limits) |
| 429 | Oui | Vous pouvez retenter sans problème suite à une erreur 429. Peut être évité en suivant le lien pour [Trop de requêtes](troubleshoot-request-rate-too-large.md).|
| 449 | Oui | Erreur temporaire qui se produit uniquement lors des opérations d’écriture, et qui peut faire suite à une nouvelle tentative sans problème. Cela peut pointer un problème de conception où un trop grand nombre d’opérations simultanées tentent de mettre à jour le même objet dans Cosmos DB. |
| 500 | Oui | L’opération a échoué en raison d’une erreur de service inattendue. Contactez le support en renseignant un [problème de support Azure](https://aka.ms/azure-support). |
| 503 | Oui | [Service indisponible](troubleshoot-service-unavailable.md). | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Insuffisance de ports Azure SNAT (PAT)

Si votre application est déployée sur des [Machines virtuelles Azure sans adresse IP publique](../../load-balancer/load-balancer-outbound-connections.md), par défaut les [ports Azure SNAT](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports) établissent des connexions avec n’importe quel point de terminaison en dehors de votre machine virtuelle. Le nombre de connexions autorisées de la machine virtuelle au point de terminaison Azure Cosmos DB est limité par la [configuration Azure SNAT](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Cette situation peut aboutir à la limitation de la connexion, à la fermeture de la connexion ou aux [délais d’expiration de la requête](troubleshoot-dot-net-sdk-request-timeout.md) mentionnés ci-dessus.

 Les ports Azure SNAT sont utilisés uniquement quand votre machine virtuelle a une adresse IP privée et tente de se connecter avec une adresse IP publique. Il existe deux solutions de contournement pour éviter la limitation d’Azure SNAT (à condition que vous utilisiez déjà une seule instance client sur l’ensemble de l’application) :

* Ajoutez votre point de terminaison de service Azure Cosmos DB au sous-réseau de votre réseau virtuel Machines virtuelles Azure. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quand le point de terminaison de service est activé, les requêtes ne sont plus envoyées d’une adresse IP publique à Azure Cosmos DB. Au lieu de cela, les identités du réseau virtuel et du sous-réseau sont envoyées. Cette modification peut entraîner des problèmes de pare-feu si seules les adresses IP publiques sont autorisées. Si vous utilisez un pare-feu, quand vous activez le point de terminaison de service, ajoutez un sous-réseau au pare-feu à l’aide de [Listes de contrôle d’accès de réseau virtuel](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Assignez une [adresse IP publique à votre machine virtuelle Azure](../../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Latence réseau élevée
Une latence réseau élevée peut être identifiée à l’aide de la [chaîne de diagnostic](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) dans le Kit de développement logiciel (SDK) V2 ou des [diagnostics](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) dans le SDK V3.

Si aucun [délai d’expiration](troubleshoot-dot-net-sdk-request-timeout.md) n’est présent et que les diagnostics affichent des demandes uniques pour lesquelles la latence élevée est évidente.

# <a name="v3-sdk"></a>[SDK V3](#tab/diagnostics-v3)

Les diagnostics peuvent être obtenus à partir de n’importe quel `ResponseMessage`, `ItemResponse`, `FeedResponse`ou `CosmosException` par la propriété `Diagnostics` :

```csharp
ItemResponse<MyItem> response = await container.CreateItemAsync<MyItem>(item);
Console.WriteLine(response.Diagnostics.ToString());
```

Les interactions réseau dans les diagnostics sont notamment :

```json
{
    "name": "Microsoft.Azure.Documents.ServerStoreModel Transport Request",
    "id": "0e026cca-15d3-4cf6-bb07-48be02e1e82e",
    "component": "Transport",
    "start time": "12: 58: 20: 032",
    "duration in milliseconds": 1638.5957
}
```

Où `duration in milliseconds` affiche la latence.

# <a name="v2-sdk"></a>[SDK V2](#tab/diagnostics-v2)

Les diagnostics sont disponibles lorsque le client est configuré en [mode direct](sql-sdk-connection-modes.md), par le biais de la propriété `RequestDiagnosticsString` :

```csharp
ResourceResponse<Document> response = await client.ReadDocumentAsync(documentLink, new RequestOptions() { PartitionKey = new PartitionKey(partitionKey) });
Console.WriteLine(response.RequestDiagnosticsString);
```

Et la latence se situe sur la différence entre `ResponseTime` et `RequestStartTime` :

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```
--- 

Cette latence peut avoir plusieurs causes :

* Votre application ne s’exécute pas dans la même région que votre compte Azure Cosmos DB.
* La configuration de votre [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) est incorrecte et tente de se connecter à une autre région que celle dans laquelle votre application est en cours d’exécution.
* Il peut y avoir un goulot d’étranglement sur l’interface réseau en raison d’un trafic élevé. Si l’application s’exécute sur des machines virtuelles Azure, voici les solutions possibles :
    * Envisagez d’utiliser une [machine virtuelle avec la mise en réseau accélérée activée](../../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Activez la [mise en réseau accélérée sur une machine virtuelle existante](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Envisagez d’utiliser un [ordinateur virtuel haut de gamme](../../virtual-machines/sizes.md).

### <a name="common-query-issues"></a>Problèmes courants liés aux requêtes

Les [métriques de requête](sql-api-query-metrics.md) vous aident à déterminer où la requête passe la majeure partie du temps. Elle vous permettent de voir quelle portion du temps est consacrée au serveur principal et au client. Découvrez-en plus sur [la résolution des problèmes liés aux performances des requêtes](troubleshoot-query-performance.md).

* Si la requête principale retourne rapidement des résultats, et passe beaucoup de temps sur le client, vérifiez la charge de la machine. Il est probable que les ressources sont insuffisantes et que le Kit de développement logiciel (SDK) attend que des ressources soient disponibles pour gérer la réponse.
* Si la requête principale est lente, essayez de l'[optimiser](troubleshoot-query-performance.md) et d'examiner la [stratégie d'indexation](../index-overview.md) actuelle.

    > [!NOTE]
    > Le processus hôte Windows 64 bits est recommandé pour améliorer les performances. Le kit de développement logiciel (SDK) SQL intègre un fichier ServiceInterop.dll natif pour analyser et optimiser les requêtes localement. ServiceInterop.dll est uniquement pris en charge sur la plateforme Windows x64. Pour Linux et les autres plateformes non prises en charge où ServiceInterop.dll n’est pas disponible, il procède à un appel réseau supplémentaire à destination de la passerelle afin d'obtenir la requête optimisée.

Si vous rencontrez l’erreur suivante : `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:`, et que vous utilisez Windows, vous devez effectuer une mise à niveau vers la version la plus récente de Windows.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)
* En savoir plus sur les [Kits SDK Java basés sur Reactor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
