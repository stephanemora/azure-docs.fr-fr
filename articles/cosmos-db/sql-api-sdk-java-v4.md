---
title: 'Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API SQL : notes de publication et ressources'
description: Découvrez le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API SQL ainsi que le Kit de développement logiciel (SDK), notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java Async SQL d’Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: aabd52d47bfc59de7a1d79bbe5ffbdda90d099bf
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90060694"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API Core (SQL) : notes de publication et ressources
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK .NET Change Feed v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [SDK Java v4](sql-api-sdk-java-v4.md)
> * [SDK Java Async v2](sql-api-sdk-async-java.md)
> * [SDK Java Sync v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [API REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Exécuteur en bloc – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

Le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 pour API Core (SQL) combine une API asynchrone et une API synchrone en un seul artefact Maven. Le Kit de développement logiciel (SDK) v4 offre des performances améliorées, de nouvelles fonctionnalités d’API et une prise en charge asynchrone basée sur le réacteur de projet et la [bibliothèque Netty](https://netty.io/). Les utilisateurs peuvent s’attendre à des performances améliorées avec le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4 par rapport au [Kit de développement logiciel (SDK) Java Azure Cosmos DB v2 asynchrone](sql-api-sdk-async-java.md) et au [Kit de développement logiciel (SDK) Java Azure Cosmos DB v2 synchrone](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Ces notes de publication concernent uniquement le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4. Si vous utilisez actuellement une version antérieure à v4, consultez le guide [Migrer votre application pour utiliser le SDK Java v4 Azure Cosmos DB](migrate-java-v4-sdk.md) afin d’obtenir de l’aide sur la mise à niveau vers v4.
>
> Voici trois étapes pour aller plus vite.
> 1. Installez le [Runtime Java minimal pris en charge, JDK 8,](/java/azure/jdk/?view=azure-java-stable) pour pouvoir utiliser le Kit de développement logiciel (SDK).
> 2. Utilisez le [guide de démarrage rapide pour le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), qui vous permet d’accéder à l’artefact Maven et de passer en revue les requêtes Azure Cosmos DB de base.
> 3. Pour optimiser le Kit de développement logiciel (SDK) pour votre application, lisez les [conseils sur les performances](performance-tips-java-sdk-v4-sql.md) et [sur le dépannage](troubleshoot-java-sdk-v4-sql.md) relatifs au Kit de développement logiciel (SDK) Java Azure Cosmos DB v4.
>
> Les [ateliers et laboratoires Azure Cosmos DB](https://aka.ms/cosmosworkshop) sont une autre formidable ressource pour apprendre à utiliser le Kit de développement logiciel (SDK) Java Azure Cosmos DB v4.
>

## <a name="helpful-content"></a>Contenu utile

| Contenu | Lien |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentation de l’API** | [Documentation de référence sur l’API Java](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Contribution au Kit de développement logiciel (SDK)** | [Référentiel centralisé relatif au Kit de développement logiciel (SDK) Azure pour Java sur GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Prise en main** | [Démarrage rapide : Créer une application Java pour gérer les données de l’API SQL d’Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [Référentiel GitHub avec code de démarrage rapide](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Exemples de code de base** | [Azure Cosmos DB : exemples Java pour l’API SQL](sql-api-java-sdk-samples.md) <br> [Référentiel GitHub avec exemple de code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Application console avec flux de modification**| [Flux de modification – Exemple de Kit de développement logiciel (SDK) Java v4](create-sql-api-java-changefeed.md) <br> [Référentiel GitHub avec exemple de code](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exemple d’application web**| [Créer une application web avec le Kit de développement logiciel (SDK) Java v4](sql-api-java-application.md) <br> [Référentiel GitHub avec exemple de code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Conseils sur les performances**| [Conseils en performances pour le Kit de développement logiciel (SDK) Java v4](performance-tips-java-sdk-v4-sql.md)| 
| **Dépannage** | [Résoudre les problèmes du Kit de développement logiciel (SDK) Java v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migration d’un ancien Kit de développement logiciel (SDK) vers la v4** | [Migrer vers le Kit de développement logiciel (SDK) Java V4](migrate-java-v4-sdk.md) |
| **Runtime minimal pris en charge**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Ateliers et laboratoires Azure Cosmos DB** |[Page d’accueil relative aux ateliers Cosmos DB](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Historique des mises en production

### <a name="450-beta1-unreleased"></a>4.5.0-beta.1 (non commercialisé)

### <a name="440-2020-09-12"></a>4.4.0 (2020-09-12)
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Correction de RequestTimeoutException lors de l’activation de la dépendance `netty-tcnative-boringssl`.
* Résolution d’un problème de fuite de mémoire sur les opérations `Delete` en mode `GATEWAY`.
* Correction d’une fuite dans l’instanciation `CosmosClient` lorsque l’URI de point de terminaison est non valide.
* Amélioration des diagnostics `CPU History`.

### <a name="431-2020-08-13"></a>4.3.1 (2020-08-13)
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Correction d’un problème avec la requête `GROUP BY`, qui renvoyait une seule page.
* Correction du format de chaîne de l’agent utilisateur pour respecter les directives centrales du kit SDK.
* Amélioration des informations de diagnostic pour inclure les diagnostics du plan de requête.

### <a name="430-2020-07-29"></a>4.3.0 (29/07/2020)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Mise à jour de la version de la bibliothèque reactor-core vers `3.3.8.RELEASE`. 
* Mise à jour de la version de la bibliothèque reactor-netty vers `0.9.10.RELEASE`. 
* Mise à jour de la version de la bibliothèque netty vers `4.1.51.Final`. 
* Ajout de nouvelles API de surcharge pour `upsertItem` avec `partitionKey`. 
* Ajout de la prise en charge du suivi de télémétrie ouverte. 
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Correction du problème à cause duquel une SSLException était levée en cas d’annulation de requêtes en mode GATEWAY.
* Correction de la stratégie de nouvelles tentatives de limitation de ressources sur l’exécution de procédures stockées.
* Correction du problème à cause duquel le kit de développement logiciel (SDK) se bloquait au niveau du journal en mode DEBUG. 
* Correction des pics périodiques de latence en mode direct. 
* Résolution du problème de temps d’initialisation élevé du client. 
* Correction du bogue du proxy HTTP lors de la personnalisation du client en mode direct et en mode passerelle. 
* Correction d’un bogue NPE potentiel quand les utilisateurs passent des options null. 
* Ajout de timeUnit à `requestLatency` dans la chaîne de diagnostic.
* Suppression de la chaîne d’URI dupliquée de la chaîne de diagnostic. 
* Correction de la chaîne de diagnostic au format JSON approprié pour les opérations de point.
* Correction du problème avec l’opérateur `.single()` provoquant un problème de chaîne de réacteur en cas d’exception Introuvable. 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ajout de l’API de journalisation des scripts à `CosmosStoredProcedureRequestOptions`.
* Mise à jour de `DirectConnectionConfig` par défaut `idleEndpointTimeout` sur 1h et de `connectTimeout` par défaut sur 5s.
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Correction d’un problème en raison duquel `GatewayConnectionConfig` `idleConnectionTimeout` remplaçait `DirectConnectionConfig` `idleConnectionTimeout`.
* Correction de `responseContinuationTokenLimitInKb` pour obtenir et définir des API dans `CosmosQueryRequestOptions`.
* Correction d’un problème dans la requête et le flux de modification lors de la recréation de la collection portant le même nom.
* Correction d’un problème avec ClassCastException.
* Correction d’un problème lié au classement par requête avec NullPointerException.
* Correction d’un problème de gestion des requêtes annulées en mode direct entraînant l’appel du réacteur `onErrorDropped`. 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ajout de la prise en charge de la requête `GROUP BY`.
* Augmentation de la valeur par défaut de maxConnectionsPerEndpoint à 130 dans DirectConnectionConfig.
* Augmentation de la valeur par défaut de maxRequestsPerConnection à 30 dans DirectConnectionConfig.
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Résolution des problèmes liés à la requête de tri retournant des résultats en double lors de la reprise à l’aide d’un jeton de continuation. 
* Résolution des problèmes liés à la requête de valeur retournant des valeurs nulles pour un objet imbriqué.
* Résolution de l’exception de pointeur nul sur le gestionnaire de requêtes dans RntbdClientChannelPool.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* `QueryRequestOptions` renommé en `CosmosQueryRequestOptions`.
* Mise à jour de `ChangeFeedProcessorBuilder` vers le modèle de générateur.
* Mise à jour de `CosmosPermissionProperties` avec le nom du nouveau conteneur et les API des ressources enfants.
* Ajout d’exemples supplémentaires et documents enrichis vers `CosmosClientBuilder`. 
* Mise à jour des API `CosmosDatabase` & `CosmosContainer` avec throughputProperties pour la prise en charge de la mise à l’échelle automatique/AutoPilot. 
* `CosmosClientException` renommé en `CosmosException`. 
* Remplacement de `AccessCondition` & `AccessConditionType` par des API `ifMatchETag()` & `ifNoneMatchETag()`. 
* Fusion de tous les types `Cosmos*AsyncResponse` & `CosmosResponse` dans un type `CosmosResponse` unique.
* `CosmosResponseDiagnostics` renommé en `CosmosDiagnostics`.  
* `FeedResponseDiagnostics` emballé dans `CosmosDiagnostics`. 
* Suppression de la dépendance `jackson` d’azure-cosmos et azure-core. 
* `CosmosKeyCredential` remplacé par le type `AzureKeyCredential`. 
* Ajout d’API `ProxyOptions` à `GatewayConnectionConfig`. 
* Mise à jour du kit de développement logiciel (SDK) pour utiliser le type `Instant` au lieu de `OffsetDateTime`. 
* Ajout d’un nouveau type d’énumération `OperationKind`. 
* `FeedOptions` renommé en `QueryRequestOptions`. 
* Ajout des API `getETag()` & `getTimestamp()` aux types `Cosmos*Properties`. 
* Ajout d’informations de `userAgent` dans `CosmosException``CosmosDiagnostics` & . 
* Mise à jour du caractère de nouvelle ligne dans `Diagnostics` vers le caractère de nouvelle ligne système. 
* Suppression des API `readAll*`, utilisez la requête Sélectionner toutes les API à la place.
* Ajout de l’API `ChangeFeedProcessor` de retard d’estimation.   
* Ajout de la prise en charge du débit de mise à l’échelle automatique/AutoPilot au kit de développement logiciel (SDK).  
* Remplacement de `ConnectionPolicy` par de nouvelles configurations de connexion. Exposition des API `DirectConnectionConfig` & `GatewayConnectionConfig` via `CosmosClientBuilder` pour les configurations de connexion en mode de passerelle et direct.
* Déplacement de `JsonSerializable` & `Resource` vers le package d’implémentation. 
* Ajout de l’API `contentResponseOnWriteEnabled` à CosmosClientBuilder qui désactive le contenu de réponse complet sur les opérations d’écriture.
* Exposition des API `getETag()` sur les types de réponse.
* Déplacement de `CosmosAuthorizationTokenResolver` vers l’implémentation. 
* Changement de nom de l’API `preferredLocations` & `multipleWriteLocations` en `preferredRegions` & `multipleWriteRegions`. 
* Mise à jour de `reactor-core` vers 3.3.5.RELEASE, de `reactor-netty` vers 0.9.7.RELEASE et de `netty` vers 4.1.49.Final. 
* Ajout de la prise en charge de `analyticalStoreTimeToLive` dans le SDK.     
* `CosmosClientException` étend `AzureException`. 
* Suppression des API `maxItemCount` & `requestContinuationToken` de `FeedOptions` pour utiliser à la place les API `byPage()` de `CosmosPagedFlux` & `CosmosPagedIterable`.
* Introduction de `CosmosPermissionProperties` sur la surface publique des API `Permission`.
* Suppression du type `SqlParameterList`, remplacé par `List`
* Correction de plusieurs fuites de mémoire dans le client TCP direct. 
* Ajout de la prise en charge des requêtes `DISTINCT`. 
* Suppression des dépendances externes sur `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text`.  
* Déplacement de `CosmosPagedFlux` & `CosmosPagedIterable` vers le package `utils`. 
* Mise à jour de netty 4.1.45. Final et project reactor vers la version 3.3.3.
* Mise à jour des contrats publics Rest pour les classes `Final`.
* Ajout de la prise en charge des diagnostics avancés pour les opérations de point.
* Mise à jour du package vers `com.azure.cosmos`
* Ajout du package `models` pour les contrats modèles/Rest
* Ajout du package `utils` pour les types `CosmosPagedFlux` & `CosmosPagedIterable`. 
* Mises à jour des API publiques pour utiliser `Duration` dans le SDK.
* Ajout de tous les contrats Rest au package `models`.
* `RetryOptions` a été renommé en `ThrottlingRetryOptions`.
* Ajout des types de pagination `CosmosPagedFlux` & `CosmosPagedIterable` pour les API de requête. 
* Ajout de la prise en charge du partage de TransportClient sur plusieurs instances de CosmosClients à l’aide d’une nouvelle API dans `CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Optimisations de requête en supprimant la sérialisation/désérialisation double. 
* Optimisations des en-têtes de réponse en supprimant les copies inutiles. 
* Optimisation de la sérialisation/désérialisation de `ByteBuffer` en supprimant les instanciations de chaîne intermédiaires.

#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Correction de l’exception de pointeur nul ConnectionPolicy `toString()`.
* Correction du problème d’analyse des résultats de requête en cas de requêtes de tri par valeur. 
* Résolution des problèmes de fuite de sockets avec le client TCP direct.
* Correction de `orderByQuery` pour le bogue de jeton de continuation.
* Résolution de bogue `ChangeFeedProcessor` pour le traitement des fractionnements de partition et lorsque la partition est introuvable.
* Résolution de bogue `ChangeFeedProcessor` lors de la synchronisation des mises à jour de bail entre différents threads.
* Correction de la condition de concurrence provoquant une exception `ArrayIndexOutOfBound` dans StoreReader

## <a name="faq"></a>Questions fréquentes (FAQ)
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).