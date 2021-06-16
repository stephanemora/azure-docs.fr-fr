---
title: Notes de publication et ressources du connecteur OLTP Azure Cosmos DB Apache Spark 3 pour l’API SQL (préversion)
description: Découvrez le connecteur OLTP Azure Cosmos DB Apache Spark 3 pour l’API SQL (préversion), notamment les dates de mise en production, les dates de mise hors service et les modifications apportées entre chaque version du kit SDK Java SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9b898ea6c12b79035d2a9e968f38d7b202f19ddc
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747632"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Connecteur OLTP Azure Cosmos DB Apache Spark 3 pour l’API Core (SQL) (préversion) : notes de publication et ressources
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK .NET Change Feed v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Kit SDK Java v4](sql-api-sdk-java-v4.md)
> * [SDK Java Async v2](sql-api-sdk-async-java.md)
> * [SDK Java Sync v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connecteur OLTP Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Connecteur OLTP Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [API REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Exécuteur en bloc – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

Le **connecteur OLTP Azure Cosmos DB Spark 3 (préversion)** fournit la prise en charge d’Apache Spark v3 pour Azure Cosmos DB à l’aide de l’API SQL.
[Azure Cosmos DB](introduction.md) est un service de base de données distribué à l’échelle mondiale qui permet aux développeurs de travailler sur des données à l’aide de diverses API standard, telles que SQL, MongoDB, Cassandra, Graph et Table.

> [!Note]
> Cette version du connecteur OLTP Azure Cosmos DB Spark 3 est une préversion.
> Aucun test de charge ou de performances n’a été effectué pour cette build.
> Cette build n’est pas recommandée pour les scénarios de production.
>

## <a name="documentation"></a>Documentation

- [Bien démarrer](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [API Catalogue](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Référence des paramètres de configuration](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Compatibilité des versions

| Connecteur     | Spark         | Version Java minimale | Versions de Scala prises en charge |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta.1  | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>Téléchargement

Vous pouvez utiliser la coordonnée Maven du jar pour installer automatiquement le connecteur Spark sur votre Databricks Runtime 8 à partir de Maven : `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

Vous pouvez également intégrer le connecteur Spark Cosmos DB dans votre projet SBT :
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Le connecteur Spark Cosmos DB est disponible dans le [référentiel central Maven](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar).

### <a name="general"></a>Général

Si vous rencontrez des bogues, signalez un problème [ici](https://github.com/Azure/azure-sdk-for-java/issues/new).

Pour suggérer une nouvelle fonctionnalité ou des modifications, signalez un problème comme vous le feriez pour un bogue.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez notre [guide de démarrage rapide pour utiliser le connecteur OLTP Azure Cosmos DB Apache Spark 3](create-sql-api-spark.md).
