---
title: Spring Data Azure Cosmos DB v3 pour les notes de publication et les ressources de l’API SQL
description: Découvrez Spring Data Azure Cosmos DB v3 pour l’API SQL, notamment les dates de mise en production, les dates de mise hors service et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java asynchrone SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9e5b92918d93109183740be555bb805877862407
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817868"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v3 pour l’API Core (SQL) : Notes de publication et ressources
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

Spring Data Azure Cosmos DB v3 pour Core (SQL) permet aux développeurs d’utiliser Azure Cosmos DB dans des applications Spring. Spring Data Azure Cosmos DB expose l’interface de données Spring pour manipuler les bases de données et les collections, travailler avec des documents et émettre des requêtes. Les API synchrones et asynchrones (réactives) sont prises en charge dans le même artefact Maven. 

Spring Data Azure Cosmos DB est dépendant de l’infrastructure Spring Data. L’équipe du Kit de développement logiciel (SDK) Azure Cosmos DB publie des artefacts Maven pour les versions 2.2 et 2.3 de Spring Data.

[Spring Framework](https://spring.io/projects/spring-framework) est un modèle de programmation et de configuration qui rationalise le développement d’applications Java. Spring rationalise la « plomberie » des applications en injectant des dépendances. De nombreux développeurs apprécient Spring, car il rend la création et le test d’applications plus simples. [Spring Boot](https://spring.io/projects/spring-boot) étend cette prise en charge de la plomberie en vue du développement d’applications web et de microservices. [Spring Data](https://spring.io/projects/spring-data) est un modèle de programmation et une infrastructure permettant l’accès à des magasins de données tels qu’Azure Cosmos DB à partir du contexte d’une application Spring ou Spring Boot. 

Vous pouvez utiliser Spring Data Azure Cosmos DB dans vos applications [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/).

> [!IMPORTANT]  
> Ces notes de publication sont destinées à la version 3 de Spring Data Azure Cosmos DB. Les [notes de publication de la version 2 sont accessibles ici](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB prend uniquement en charge l’API SQL.
>
> Pour plus d’informations concernant Spring Data sur d’autres API d’Azure Cosmos DB, consultez les articles suivants :
> * [Spring Data pour Apache Cassandra avec Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB avec Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin avec Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Démarrer ici

# <a name="explore"></a>[Explorer](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Ces onglets contiennent des exemples Spring Data Azure Cosmos DB de base.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Configurer des dépendances

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Connexion](#tab/connect)

### <a name="connect"></a>Se connecter

Spécifiez les détails du compte et du conteneur Azure Cosmos DB. Spring Data Azure Cosmos DB crée automatiquement le client et se connecte au conteneur.

[application.properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties) :
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Ops doc](#tab/docs)

### <a name="document-operations"></a>Opérations de document

Créer :
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Create":::

Supprimer :
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Delete":::

# <a name="query"></a>[Requête](#tab/queries)

Requête :
:::code language="java" source="~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java" ID="Query":::

---

## <a name="resources"></a>Ressources

* **Contribution au Kit de développement logiciel (SDK)**  : [Référentiel Spring Data Azure Cosmos DB sur GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Tutoriel** : [Didacticiel Spring Data Azure Cosmos DB sur GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

## <a name="release-history"></a>Historique des mises en production

### <a name="300-beta2-september-17-2020"></a>3.0.0-beta.2 (17 septembre 2020)

#### <a name="new-features"></a>Nouvelles fonctionnalités

* Mise à jour de l’ID d’artefact en `azure-spring-data-cosmos`.
* Mise à jour de la dépendance azure-cosmos en `4.5.0`.
* Prise en charge de `Query Annotation` pour les requêtes natives.
* Prise en charge de Java 11.
* Ajout de la prise en charge de la clé de partition imbriquée en exposant le champ `partitionKeyPath` dans l’annotation `@Container`.
* Ajout de la prise en charge du type de requête `limit` autorisant l’utilisation de `top` et de `first` lors de la définition des API de référentiel.

#### <a name="key-bug-fixes"></a>Principaux bogues résolus

* Correction du bogue de clé de partition imbriquée en cas d’utilisation avec l’annotation `@GeneratedValue`.

### <a name="300-beta1-august-17-2020"></a>3.0.0-beta.1 (17 août 2020)

#### <a name="new-features"></a>Nouvelles fonctionnalités

* Met à jour de l’ID de groupe en `com.azure`.
* Met à jour de l’ID d’artefact en `azure-spring-data-2-3-cosmos`.
* Met à jour de la dépendance du Kit de développement logiciel (SDK) azure-cosmos en `4.3.2-beta.2`.
* Ajoute la prise en charge des entités d’audit : la gestion automatique des champs annotés `createdBy`, `createdDate`, `lastModifiedBy` et `lastModifiedDate`.
* Ajoute la prise en charge de l’annotation `@GeneratedValue` pour la génération automatique d’ID pour les champs d’ID de type `String`.
* Ajoute la prise en charge de la configuration multibase de données pour des comptes Azure Cosmos DB uniques avec plusieurs bases de données et pour des comptes Azure Cosmos DB multiples avec plusieurs bases de données.
* Ajoute la prise en charge de l’annotation `@Version` sur n’importe quel champ de chaîne.
* Met à jour les types de retour de l’API de synchronisation en types `Iterable` au lieu de `List`.
* Expose `CosmosClientBuilder` du Kit de développement logiciel (SDK) Azure Cosmos DB en tant que composant Spring à la classe `@Configuration`.
* Met à jour `CosmosConfig` pour contenir les métriques de requête et l’implémentation du processeur de diagnostics des réponses.
* Ajoute la prise en charge du renvoi du type de données `Optional` pour les requêtes à résultat unique.

#### <a name="renames"></a>Renommages

* `CosmosDbFactory` en `CosmosFactory`.
* `CosmosDBConfig` en `CosmosConfig`.
* `CosmosDBAccessException` en `CosmosAccessException`.
* Annotation `Document` en annotation `Container`.
* Annotation `DocumentIndexingPolicy` en annotation `CosmosIndexingPolicy`.
* `DocumentQuery` en `CosmosQuery`.
* Indicateur application.properties `populateQueryMetrics` en `queryMetricsEnabled`.

#### <a name="key-bug-fixes"></a>Principaux bogues résolus

* Planification de la tâche de journalisation des diagnostics dans les threads `Parallel` afin d’éviter de bloquer les threads d’E/S de Netty.
* Correction du verrouillage optimiste lors de l’opération de suppression.
* Correction du problème lié à l’échappement des requêtes pour la clause `IN`.
* Correction du problème en autorisant le type de données `long` pour `@Id`.
* Correction du problème en autorisant `boolean`, `long`, `int` et `double` comme types de données pour l’annotation `@PartitionKey`.
* Correction des mots clés `IgnoreCase` et `AllIgnoreCase` pour les requêtes Ignorer la casse.
* Suppression de la valeur d’unité de requête par défaut de 4 000 lorsque les conteneurs sont créés automatiquement.

## <a name="faq"></a>Questions fréquentes (FAQ)

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Apprenez-en davantage sur [Spring Framework](https://spring.io/projects/spring-framework).

Apprenez-en davantage sur [Spring Boot](https://spring.io/projects/spring-boot).

Apprenez-en davantage sur [Spring Data](https://spring.io/projects/spring-data).