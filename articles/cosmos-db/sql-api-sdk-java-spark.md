---
title: Notes de publication et ressources Azure Cosmos DB Apache Spark Connector pour API Core (SQL)
description: Découvrez Azure Cosmos DB Apache Spark Connector pour l’API SQL, notamment les dates de mise en production, dates de mise hors service et modifications apportées entre chaque version du kit de développement logiciel (SDK) Java asynchrone SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590007"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark Connector pour API Core (SQL) : notes de publication et ressources
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

Accélérer l’analytique en temps réel des Big Data au moyen d’Azure Cosmos DB Apache Spark Connector pour Core (SQL). Spark Connector vous permet d’exécuter des tâches [Spark](https://spark.apache.org/) sur les données stockées dans Azure Cosmos DB. Le traitement par lots et des flux de données sont pris en charge.

Vous pouvez utiliser le connecteur avec [Azure Databricks](https://azure.microsoft.com/services/databricks) ou [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), qui fournissent des clusters Spark gérés sur Azure. Le tableau suivant renseigne sur les versions Spark prises en charge.

| Composant | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x et 2.1.x |
| Scala | 2.11 |
| Version du runtime Azure Databricks | > 3.4 |

> [!WARNING]
> Ce connecteur prend en charge l’API de base (SQL) d’Azure Cosmos DB.
> Pour l’API Cosmos DB pour MongoDB, utilisez le [connecteur MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Pour l’API Cosmos DB pour Cassandra, utilisez le [connecteur Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Contenu de l’aide

| Contenu | Lien |
|---|---|
| **Téléchargement du Kit de développement logiciel (SDK)** | [Télécharger à partir d’Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Documentation de l’API** | [Référence Spark Connector]() |
|**Contribution au Kit de développement logiciel (SDK)** | [Azure Cosmos DB Connector pour Apache Spark sur GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Prise en main** | [Accélérer l’analytique en temps réel des Big Data au moyen du connecteur Apache Spark-Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Utiliser Apache Spark Structured Streaming avec Apache Kafka et Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Historique des mises en production

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige un cas limite de point de contrôle de diffuser en continu où « ID » contient le caractère « | » avec la configuration « ChangeFeedMaxPagesPerBatch » appliquée

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ajoute la prise en charge des mises à jour en bloc lors de l’utilisation de clés de partition imbriquées
* Ajoute la prise en charge des types de données Decimal et Float lors des opérations d’écriture dans Cosmos DB.
* Ajoute la prise en charge des types Timestamp lors de l’utilisation de la valeur Long (unix Epoch)
#### <a name="key-bug-fixes"></a>Principaux bogues résolus

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige l’exception de forçage de type lors de l’utilisation de la configuration « WriteThroughputBudget ».

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ajoute des informations d’erreur pour les échecs en bloc à l’exception et au journal.
#### <a name="key-bug-fixes"></a>Principaux bogues résolus

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige les problèmes de point de contrôle de diffusion en continu.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige le niveau de journalisation d’un message laissé involontairement avec le niveau ERROR pour réduire le bruit

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige un bogue affectant la diffusion en continu structurée pendant les fractionnements de partition, pouvant entraîner des enregistrements de flux de modification manquants ou l’affichage d’exceptions Null pour les écritures de point de contrôle

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige un bogue dans lequel un schéma personnalisé fourni pour readStream est ignoré

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige la régression (le fichier JAR sans ombrage inclut toutes les dépendances ombrées) qui a augmenté le temps de génération de 50 %

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Principaux bogues résolus
* Corrige un problème de dépendance entraînant un échec Direct Transport over TCP avec RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Améliore la gestion des connexions et le regroupement de connexions pour réduire le nombre d’appels de métadonnées
#### <a name="key-bug-fixes"></a>Principaux bogues résolus

## <a name="faq"></a>Questions fréquentes (FAQ)
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Pour en savoir plus sur Apache Spark, consultez la [page d’accueil](https://spark.apache.org/).