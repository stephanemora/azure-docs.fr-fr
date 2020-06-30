---
title: Interrogation du magasin analytique Azure Cosmos DB (préversion) avec Apache Spark pour Azure Synapse Analytics
description: Comment interroger le magasin analytique Azure Cosmos DB avec Apache Spark pour Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 399aa00be3d585b1d37bab95d8108ee8a09a8791
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85195009"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Interrogation du magasin analytique Azure Cosmos DB (préversion) avec Apache Spark pour Azure Synapse Analytics

Cet article donne des exemples de la façon dont vous pouvez interagir avec le magasin analytique à partir de mouvements Synapse. Les mouvements sont visibles lorsque vous cliquez avec le bouton droit sur un conteneur. Avec les mouvements, vous pouvez rapidement générer du code et l’adapter à vos besoins. Les mouvements sont également bien adaptés pour découvrir des données en un seul clic.

## <a name="load-to-dataframe"></a>Charger dans le tramedonnées

Dans cette étape, vous allez lire des données à partir du magasin analytique Azure Cosmos DB dans un tableau Spark. Il affiche 10 lignes du tramedonnées appelé ***df***. Une fois vos données dans le tramedonnées, vous pouvez effectuer des analyses supplémentaires.

Cette opération n’a aucun impact sur le magasin transactionnel.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

Le mouvement de code équivalent dans **Scala** correspond au code suivant :
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Créer une table Spark

Dans ce mouvement, vous allez créer une table Spark pointant vers le conteneur que vous avez sélectionné. Cette opération n’entraîne aucun déplacement de données. Si vous décidez de supprimer cette table, le conteneur sous-jacent (et le magasin analytique correspondant) ne seront pas affectés. 

Ce scénario est pratique pour réutiliser des tables à l’aide d’outils tiers et fournir une accessibilité aux données à l’exécution.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Écrire un tramedonnées dans un conteneur

Dans ce mouvement, vous allez écrire un tramedonnées dans un conteneur. Cette opération aura un impact sur les performances transactionnelles et consommera des RU. Les performances transactionnelles Azure Cosmos DB sont parfaitement adaptées aux transactions d’écriture. Veillez à remplacer **YOURDATAFRAME** par le tramedonnées dans lequel vous souhaitez écrire.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Le mouvement de code équivalent dans **Scala** correspond au code suivant :
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Charger la diffusion en continu de tramedonnées à partir d’un conteneur
Dans ce mouvement, vous utiliserez la fonctionnalité de diffusion en continu Spark pour charger des données à partir d’un conteneur dans un tramedonnées. Les données seront stockées dans le compte du lac de données principal (et le système de fichiers) que vous avez connecté à l’espace de travail. 

Si le dossier */localReadCheckpointFolder* n’existe pas, il est créé automatiquement. Cette opération aura un impact sur les performances transactionnelles d’Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Le mouvement de code équivalent dans **Scala** correspond au code suivant :
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Écrire la diffusion en continu de tramedonnées vers un conteneur
Dans ce mouvement, vous allez écrire un tramedonnées de diffusion en continu dans le conteneur Azure Cosmos DB que vous avez sélectionné. Si le dossier */localReadCheckpointFolder* n’existe pas, il est créé automatiquement. Cette opération aura un impact sur les performances transactionnelles d’Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Le mouvement de code équivalent dans **Scala** correspond au code suivant :
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Étapes suivantes

* [Découvrir ce qui est pris en charge entre Synapse et Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Se connecter à Synapse Link pour Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
