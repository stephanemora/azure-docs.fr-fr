---
title: 'API SQL Azure Cosmos DB : Exemples du kit SDK Java v4'
description: Recherchez des exemples Java sur GitHub pour les tâches courantes utilisant l’API SQL Azure Cosmos DB, y compris les opérations CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: db291b735ed6456a007446ad23373ff25e806aab
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727159"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>API SQL Azure Cosmos DB : Exemples du kit SDK Java v4

> [!div class="op_single_selector"]
> * [Exemples du kit SDK .NET V2](sql-api-dotnet-samples.md)
> * [Exemples du kit SDK .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Exemples du kit SDK Java V4](sql-api-java-sdk-samples.md)
> * [Exemples Node.js](sql-api-nodejs-samples.md)
> * [Exemples Python](sql-api-python-samples.md)
> * [Galerie d’exemples de code Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Pour en savoir plus sur le kit SDK Java v4, consultez les [Notes de publication](sql-api-sdk-java-v4.md) du kit SDK Java v4 Azure Cosmos DB, le [Référentiel Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), les [Conseils en matière de performances](performance-tips-java-sdk-v4-sql.md) du kit SDK Java v4 Azure Cosmos DB et le [Guide de résolution des problèmes](troubleshoot-java-sdk-v4-sql.md) du kit SDK Java v4 Azure Cosmos DB. Si vous utilisez actuellement une version antérieure à v4, consultez le guide [Migrer vers le kit SDK Java v4 Azure Cosmos DB](migrate-java-v4-sdk.md) pour vous accompagner dans la mise à niveau vers v4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Vous pouvez [activer les avantages d’abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) : Votre abonnement Visual Studio vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Le dépôt GitHub [azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) contient la dernière version des exemples d’applications qui exécutent des opérations CRUD, ainsi que d’autres opérations courantes sur les ressources Azure Cosmos DB. Cet article fournit :

* Des liens vers les tâches dans chacun des exemples de fichiers de projet Java. 
* Des liens vers le contenu de référence d’API connexe.

**Composants requis**

Pour exécuter cet exemple d’application, vous avez besoin de ce qui suit :

* Kit de développement Java 8
* Kit SDK Java v4 Azure Cosmos DB

Vous pouvez également utiliser Maven pour obtenir les tout derniers fichiers binaires du kit SDK Java v4 Azure Cosmos DB et les utiliser dans votre projet. Maven ajoute automatiquement toutes les dépendances nécessaires. Sinon, vous pouvez directement télécharger les dépendances répertoriées dans le fichier pom.xml et les ajouter à votre chemin d’accès de build.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Exécution des exemples d'applications**

Clonez l’exemple de référentiel :
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Vous pouvez exécuter les exemples à l’aide d’un environnement de développement intégré (Eclipse, IntelliJ ou VSCODE), ou depuis la ligne de commande en utilisant Maven.

Ces variables d’environnement doivent être définies

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

afin d’accorder aux exemples un accès en lecture/écriture à votre compte.

Pour exécuter un exemple, précisez sa classe principale 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

sachant que *sample.synchronicity.MainClass* peut être
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcessor *(Changefeed n’a qu’un exemple asynchrone, pas d’exemple synchrone.)* ...etc...

> [!NOTE]
> Chaque exemple est autonome, se définit lui-même et se nettoie automatiquement. Les exemples émettent plusieurs appels pour créer un élément `CosmosContainer`. À chaque appel, votre abonnement est facturé pour 1 heure d’utilisation selon le niveau de performances de la collection créée. 
> 
> 

## <a name="database-examples"></a>Exemples de base de données
Le fichier [Exemples CRUD de base de données](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) montre comment effectuer les tâches suivantes. Pour en savoir plus sur les bases de données Azure Cosmos avant d’exécuter les exemples suivants, consultez l’article conceptuel [Utiliser des bases de données, des conteneurs et des éléments](databases-containers-items.md). 

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une base de données](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L76-L84) | CosmosClient.createDatabaseIfNotExists |
| [Lire une base de données par identifiant](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L86-L94) | CosmosClient.getDatabase |
| [Lire toutes les bases de données](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L96-L111) | CosmosClient.readAllDatabases |
| [Supprimer une base de données](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L113-L122) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Exemples de collection
Le fichier [Exemples CRUD de collection](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) montre comment effectuer les tâches suivantes. Pour en savoir plus sur les collections Azure Cosmos avant d’exécuter les exemples suivants, consultez l’article conceptuel [Utiliser des bases de données, des conteneurs et des éléments](databases-containers-items.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d'une collection](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L94-106) | CosmosDatabase.createContainerIfNotExists |
| [Modifier les performances configurées d’une collection](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L108-116) | CosmosContainer.replaceProvisionedThroughput |
| [Obtenir une collection par identifiant](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L118-126) | CosmosDatabase.getContainer |
| [Lire toutes les collections d’une base de données](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L128-143) | CosmosDatabase.readAllContainers |
| [Supprimer une collection](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L145-154) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Exemples de collection avec mise à l’échelle automatique

Pour en savoir plus sur la mise à l’échelle automatique avant d’exécuter ces exemples, consultez les instructions suivantes qui vous permettent d’activer la mise à l’échelle automatique de votre [compte](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) et celle de vos [bases de données et conteneurs](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

Le fichier [Exemples CRUD de base de données avec mise à l’échelle automatique](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) montre comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une base de données avec un débit maximal de mise à l’échelle automatique spécifié](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L76-L87) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

Le fichier [Exemples CRUD de collection avec mise à l’échelle automatique](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) montre comment effectuer les tâches suivantes. 

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une collection avec le débit maximal de mise à l’échelle automatique spécifié](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L95-L107) | CosmosDatabase.createContainerIfNotExists |
| [Modifier le débit maximal de mise à l’échelle automatique configuré d’une collection](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L109-L117) | CosmosContainer.replaceThroughput |
| [Lire la configuration du débit de mise à l’échelle automatique d’une collection](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L119-L130) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Exemples de collection de stockage analytique

Le fichier [Exemples CRUD de collection de stockage analytique](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) montre comment effectuer les tâches suivantes. Pour en savoir plus sur les collections Azure Cosmos avant d’exécuter les exemples suivants, consultez le Magasin analytique et Synapse Azure Cosmos DB.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d'une collection](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L94-L109) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Exemples de document
Le fichier [Exemples CRUD de document](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) montre comment effectuer les tâches suivantes. Pour en savoir plus sur les documents Azure Cosmos avant d’exécuter les exemples suivants, consultez l’article conceptuel [Utiliser des bases de données, des conteneurs et des éléments](databases-containers-items.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer un document](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L127-L141) | CosmosContainer.createItem |
| [Lire un document par identifiant](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L143-L154) | CosmosContainer.readItem |
| [Lire tous les documents d’une collection](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L156-171) | CosmosContainer.readAllItems |
| [Rechercher des documents](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L173-L187) | CosmosContainer.queryItems |
| [Remplacer un document](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L189-L204) | CosmosContainer.replaceItem |
| [Effectuer une opération Upsert sur un document](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L206-L219) | CosmosContainer.upsertItem |
| [Supprimer un document](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L302-L310) | CosmosContainer.deleteItem |
| [Remplacer un document par la vérification conditionnelle de l’ETag](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L221-L261) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Lire le document uniquement s’il a changé](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L263-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Exemples d'indexation
Le fichier [Exemples CRUD de collection](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) montre comment effectuer les tâches suivantes. Pour en savoir plus sur l’indexation dans Azure Cosmos DB avant d’exécuter les exemples suivants, consultez les articles conceptuels sur les [stratégies d’indexation](index-policy.md), les [types d’indexation](index-types.md) et les [chemins d’indexation](index-paths.md). 

| Tâche | Informations de référence sur l'API |
| --- | --- |
| Exclure un document de l’index | ExcludedIndex<br>IndexingPolicy |
| Utiliser l’indexation différée | IndexingPolicy.IndexingMode |
| [Inclure dans l’index les chemins des documents spécifiés](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L142-L147) | IndexingPolicy.IncludedPaths |
| [Exclure de l’index les chemins des documents spécifiés](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L149-L154) | IndexingPolicy.ExcludedPaths |
| [Créer un index composite](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L170-L188) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Forcer une opération d’analyse de plage sur un chemin de hachage indexé | FeedOptions.EnableScanInQuery |
| Utiliser des index de plage sur des chaînes | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Effectuer une transformation d’index | - |
| [Créer un index géospatial](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L156-L168) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Pour plus d’informations sur l’indexation, consultez [Stratégies d’indexation d’Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Exemples de requêtes
Le fichier [Exemples de requête](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) indique comment effectuer les tâches suivantes à l’aide de la syntaxe de requête SQL. Pour en savoir plus sur les informations de référence sur les requêtes SQL dans Azure Cosmos DB avant d’exécuter les exemples suivants, consultez [Exemples de requêtes SQL pour Azure Cosmos DB](how-to-sql-query.md). 

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Rechercher tous les documents](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L194-L198) | CosmosContainer.queryItems |
| [Demander une égalité à l’aide de ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L279-183) | CosmosContainer.queryItems |
| [Demander une inégalité à l’aide des opérateurs != et NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L285-L293) | CosmosContainer.queryItems |
| [Interroger à l’aide d’opérateurs de plage comme &gt;, &lt;&gt;, =, &lt; =](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L295-L300) | CosmosContainer.queryItems |
| [Interroger en utilisant des opérateurs de plage sur des chaînes](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L302-L307) | CosmosContainer.queryItems |
| [Soumettre des requêtes avec la clause ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L309-L314) | CosmosContainer.queryItems |
| [Requête avec DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L316-L321) | CosmosContainer.queryItems |
| [Soumettre des requêtes avec des fonctions d’agrégation](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L323-L331) | CosmosContainer.queryItems |
| [Utiliser des sous-documents](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L333-L341) | CosmosContainer.queryItems |
| [Interroger avec des jointures à l’intérieur d’un document](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L343-L365) | CosmosContainer.queryItems |
| [Effectuer des requêtes à l’aide d’opérateurs de chaîne, d’opérateurs mathématiques et d’opérateurs de tableau](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L367-L378) | CosmosContainer.queryItems |
| [Interroger avec un fichier SQL paramétrable à l’aide de SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L380-L409) |CosmosContainer.queryItems |
| [Lancer des requêtes avec pagination explicite](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L200-L254) | CosmosContainer.queryItems |
| [Interroger les collections partitionnées en parallèle](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L256-L277) | CosmosContainer.queryItems |
| Effectuer des requêtes avec ORDER BY pour les collections partitionnées | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Exemples de flux de modification 
Le fichier [Exemple de processeur de flux de modification](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) montre comment effectuer les tâches suivantes. Pour en savoir plus sur les flux de modification dans Azure Cosmos DB avant d’exécuter les exemples suivants, consultez [Lire le flux de modification Azure Cosmos DB](read-change-feed.md) et [Processeur de flux de modification](change-feed-processor.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Fonctionnalité de flux de modification de base](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Lire le flux de modification à partir d’un moment précis | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Lire le flux de modification à partir du début](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) | - |

## <a name="server-side-programming-examples"></a>Exemples de programmation côté serveur

Le fichier [Exemple de procédure stockée](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) montre comment effectuer les tâches suivantes. Pour en savoir plus sur la programmation côté serveur dans Azure Cosmos DB avant d’exécuter les exemples suivants, consultez [Procédures stockées, déclencheurs et fonctions définies par l’utilisateur](stored-procedures-triggers-udfs.md).

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une procédure stockée](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L124-L143) | CosmosScripts.createStoredProcedure |
| [Exécuter une procédure stockée](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L160-L174) | CosmosStoredProcedure.execute |
| [Supprimer une procédure stockée](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L176-L186) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Exemples de gestion des utilisateurs
Le fichier Exemple de gestion des utilisateurs montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| Créer un utilisateur | - |
| Définir les autorisations sur une collection ou un document | - |
| Obtenir la liste des autorisations d’un utilisateur |- |