---
title: Azure Data Explorer (Kusto)
description: Cet article explique comment utiliser le connecteur pour déplacer des données entre Azure Data Explorer (Kusto) et des pools Apache Spark serverless.
services: synapse-analytics
ms.author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/19/2020
ms.reviewer: ManojRaheja
author: midesa
ms.openlocfilehash: 5b7707354d7bf63671e21d1adbaebc2ef8d71d0e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952243"
---
# <a name="azure-data-explorer-kusto-connector-for-apache-spark"></a>Connecteur Azure Data Explorer (Kusto) pour Apache Spark
Le connecteur Azure Data Explorer (Kusto) pour Apache Spark est conçu pour transférer efficacement des données entre des clusters Kusto et Spark. Ce connecteur est disponible dans les langages Python, Java et .NET. Il est intégré au runtime Azure Synapse Apache Spark 2.4.

## <a name="authentication"></a>Authentification
Lorsque vous utilisez des notebooks Microsoft Azure Synapse Analytics ou des définitions de tâche Apache Spark, l’authentification entre les systèmes se fait sans heurts grâce au service lié. Le service de jetons se connecte à Azure Active Directory pour obtenir des jetons de sécurité à utiliser lors de l’accès au cluster Kusto.

Pour les pipelines Azure Synapse Analytics, l’authentification utilisera le nom du principal de service. Actuellement, les identités managées ne sont pas prises en charge avec le connecteur Azure Data Explorer.

## <a name="prerequisites"></a>Prérequis 
  - [Connectez-vous à Azure Data Explorer](../../quickstart-connect-azure-data-explorer.md) : Vous devez configurer un service lié pour vous connecter à un cluster Kusto existant.

## <a name="limitations"></a>Limites
  - Le connecteur Azure Data Explorer (Kusto) est actuellement pris en charge uniquement sur le runtime Azure Synapse Apache Spark 2.4.
  - Le service lié Azure Data Explorer ne peut être configuré qu’avec le nom du principal de service.
  - Dans les notebooks Azure Synapse Analytics ou les définitions de tâche Apache Spark, le connecteur Azure Data Explorer utilise le Pass-through AAD pour se connecter au cluster Kusto.


## <a name="use-the-azure-data-explorer-kusto-connector"></a>Utiliser le connecteur Azure Data Explorer (Kusto)
La section suivante fournit un exemple simple de la façon d’écrire des données dans une table Kusto et de lire des données à partir d’une table Kusto. Pour obtenir la documentation détaillée, consultez le [projet de connecteur Azure Data Explorer (Kusto)](https://github.com/Azure/azure-kusto-spark). 

### <a name="read-data"></a>Lire les données

```python
kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .load()

display(kustoDf)
```

Vous pouvez également lire par lot avec le mode de distribution forcée et d’autres options avancées. Pour plus d’informations, vous pouvez vous reporter à la [référence des options de la source Kusto](https://github.com/Azure/azure-kusto-spark/blob/master/connector/src/main/scala/com/microsoft/kusto/spark/datasource/KustoSourceOptions.scala).

```python
crp = sc._jvm.com.microsoft.azure.kusto.data.ClientRequestProperties()
crp.setOption("norequesttimeout",True)
crp.toString()

kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .option("clientRequestPropertiesJson", crp.toString()) \
            .option("readMode", 'ForceDistributedMode') \
            .load()

display(kustoDf) 
```
### <a name="write-data"></a>Écrire des données

```python
df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .mode("Append") \
    .save()
```
En outre, vous pouvez également écrire des données par lot en fournissant des propriétés d’ingestion supplémentaires. Pour plus d’informations sur les propriétés d’ingestion prises en charge, vous pouvez consulter la [documentation de référence sur les propriétés d’ingestion Kusto](/azure/data-explorer/ingestion-properties).


 ```python
extentsCreationTime = sc._jvm.org.joda.time.DateTime.now().plusDays(1)
csvMap = "[{\"Name\":\"ColA\",\"Ordinal\":0},{\"Name\":\"ColB\",\"Ordinal\":1}]"
# Alternatively use an existing csv mapping configured on the table and pass it as the last parameter of SparkIngestionProperties or use none


sp = sc._jvm.com.microsoft.kusto.spark.datasink.SparkIngestionProperties(
        False, ["dropByTags"], ["ingestByTags"], ["tags"], ["ingestIfNotExistsTags"], extentsCreationTime, csvMap, None)

df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .option("sparkIngestionPropertiesJson", sp.toString()) \
    .option("tableCreateOptions","CreateIfNotExist") \
    .mode("Append") \
    .save()
```

## <a name="next-steps"></a>Étapes suivantes
- [Se connecter à l’Explorateur de données Azure](../../quickstart-connect-azure-data-explorer.md)
- [Connecteur Azure Data Explorer (Kusto) Apache Spark](https://github.com/Azure/azure-kusto-spark)