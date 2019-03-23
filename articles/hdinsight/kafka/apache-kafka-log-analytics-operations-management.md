---
title: Journaux d’analyse Azure pour Apache Kafka - Azure HDInsight
description: Découvrez comment utiliser les journaux d’Azure Monitor pour analyser les journaux du cluster Apache Kafka sur Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 5154058bb1562b670e24f8813e979588a536f086
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359855"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analyser les journaux pour Apache Kafka sur HDInsight

Découvrez comment utiliser les journaux d’Azure Monitor pour analyser les journaux générés par Apache Kafka sur HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Activer les journaux d’Azure Monitor pour Apache Kafka

Les étapes pour activer les journaux d’Azure Monitor pour HDInsight sont les mêmes pour tous les clusters HDInsight. Utilisez les liens suivants pour apprendre à créer et à configurer les services requis :

1. Créez un espace de travail Log Analytics. Pour plus d’informations, consultez le document [Prise en main d’un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Créez un cluster Kafka dans HDInsight. Pour plus d’informations, consultez le document [Démarrer avec Apache Kafka dans HDInsight](apache-kafka-get-started.md).

3. Configurer le cluster Kafka pour utiliser des journaux Azure Monitor. Pour plus d’informations, consultez le [consigne d’utiliser Azure Monitor pour surveiller HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) document.

    > [!NOTE]  
    > Vous pouvez également configurer le cluster pour utiliser des journaux Azure Monitor à l’aide de la `Enable-AzHDInsightOperationsManagementSuite` applet de commande. Cette applet de commande requiert les informations suivantes :
    >
    > * Le nom du cluster HDInsight.
    > * L’ID de l’espace de travail pour les journaux d’Azure Monitor. Vous trouverez l’ID de l’espace de travail dans votre espace de travail Log Analytics.
    > * La clé primaire pour la connexion d’analytique de journal. Pour trouver la clé primaire, ouvrez l’espace de travail dans le portail Azure, puis sélectionnez __Paramètres avancés__ dans le menu de gauche. Dans Paramètres avancés, sélectionnez __Sources connectées__>__Serveurs Linux__.


> [!IMPORTANT]  
> Il peut prendre environ 20 minutes avant que les données sont disponibles pour les journaux d’Azure Monitor.

## <a name="query-logs"></a>Journaux des requêtes

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail Log Analytics.

2. Sélectionnez __Recherche de journaux__. À ce stade, vous pouvez rechercher les données collectées par Kafka. Voici quelques exemples de recherches :

   * Utilisation du disque : `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * Utilisation du processeur : `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * Messages entrants par seconde : `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

   * Octets entrants par seconde : `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

   * Octets sortants par seconde : `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

     > [!IMPORTANT]  
     > Remplacez les valeurs de la requête par les informations spécifiques à votre cluster. Par exemple, `ClusterName_s` doit être définie sur le nom de votre cluster. La valeur `HostName_s` doit être définie sur le nom de domaine d’un nœud de travail dans le cluster.

     Vous pouvez également entrer `*` pour rechercher tous les types consignés. Actuellement, les fichiers journaux suivants sont disponibles pour les requêtes :

     | Type de journal | Description |
     | ---- | ---- |
     | log\_kafkaserver\_CL | Kafka broker server.log |
     | log\_kafkacontroller\_CL | Kafka broker controller.log |
     | metrics\_kafka\_CL | Mesures JMX Kafka |

     ![Image de la recherche de l’utilisation du processeur](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Monitor, consultez le [vue d’ensemble d’Azure Monitor](../../log-analytics/log-analytics-get-started.md) document.

Pour plus d’informations sur l’utilisation d’Apache Kafka, consultez les documents suivants :

 * [Mettre en miroir Apache Kafka entre plusieurs clusters HDInsight](apache-kafka-mirroring.md)
 * [Augmenter la scalabilité d’Apache Kafka sur HDInsight](apache-kafka-scalability.md)
 * [Utiliser le streaming Apache Spark (DStreams) avec Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Utiliser Apache Spark Structured Streaming avec Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
