---
title: Intégrer Apache Spark - Azure Event Hubs | Microsoft Docs
description: Cet article montre comment intégrer Apache Spark pour activer Structured Streaming avec Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311680"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Intégration d’Apache Spark avec Azure Event Hubs

Azure Event Hubs s’intègre parfaitement dans [Apache Spark](https://spark.apache.org/) pour permettre la génération d’applications de diffusion en continu distribuées. Cette intégration prend en charge [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html) et le [Streaming structuré](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Le connecteur Event Hubs pour Apache Spark est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-spark). Cette bibliothèque est également disponible pour les projets Maven à partir du [Référentiel central Maven](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Cet article explique comment créer une application continue dans [Azure Databricks](https://azure.microsoft.com/services/databricks/). Cet article utilise Azure Databricks, mais les clusters Spark sont également disponibles avec [HDInsight](../hdinsight/spark/apache-spark-overview.md).

L’exemple de cet article utilise deux blocs-notes Scala : un pour le streaming des événements à partir d’un hub d’événements et l’autre pour le renvoi d’événements vers celui-ci.

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Une instance Event Hubs. Si vous n’en avez pas, [créez-en une](event-hubs-create.md).
* Une instance [Azure Databricks](https://azure.microsoft.com/services/databricks/). Si vous n’en avez pas, [créez-en une](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Créez une bibliothèque à l’aide des coordonnées Maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package) : `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Diffusez des événements à partir de votre hub d’événements avec le code suivant :

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
Le code suivant envoie des événements à votre hub d’événements avec les API de lot Spark. Vous pouvez également écrire une requête de streaming pour envoyer des événements au hub d’événements :

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment configurer un flux évolutif à tolérance de panne avec le connecteur Event Hubs pour Apache Spark. Pour obtenir plus d’informations sur l’utilisation d’Event Hubs avec la diffusion en continu de flux structuré et la diffusion en continu Spark Streaming, suivez ces liens :

* [Guide d’intégration pour la diffusion en continu de flux structuré + Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Guide d’intégration pour Spark Streaming + Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
