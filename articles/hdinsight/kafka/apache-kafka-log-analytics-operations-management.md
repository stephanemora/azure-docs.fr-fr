---
title: Log Analytics pour Apache Kafka - Azure HDInsight
description: Découvrez comment utiliser Log Analytics pour analyser les journaux du cluster Apache Kafka sur Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 809196411df56c7448a9d0f983c17c60d5d3b4ae
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015415"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analyser les journaux pour Apache Kafka sur HDInsight

Découvrez comment utiliser Log Analytics pour analyser les journaux générés par Apache Kafka sur HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Activer Log Analytics pour Kafka

Les étapes permettant d’activer Log Analytics pour HDInsight sont les mêmes pour tous les clusters HDInsight. Utilisez les liens suivants pour apprendre à créer et à configurer les services requis :

1. Créez un espace de travail Log Analytics. Pour plus d’informations, consultez le document [Prise en main d’un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Créez un cluster Kafka dans HDInsight. Pour plus d’informations, consultez le document [Démarrer avec Apache Kafka dans HDInsight](apache-kafka-get-started.md).

3. Configurez le cluster Kafka pour utiliser Log Analytics. Pour plus d’informations, consultez le document [Utiliser Log Analytics pour surveiller HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md).

    > [!NOTE]
    > Vous pouvez également configurer le cluster pour utiliser Log Analytics à l’aide de l’applet de commande `Enable-AzureRmHDInsightOperationsManagementSuite`. Cette applet de commande requiert les informations suivantes :
    >
    > * Le nom du cluster HDInsight.
    > * L’ID de l’espace de travail pour Log Analytics. Vous trouverez l’ID de l’espace de travail dans votre espace de travail Log Analytics.
    > * La clé primaire de la connexion Log Analytics. Pour trouver la clé primaire, ouvrez l’espace de travail dans le portail Azure, puis sélectionnez __Paramètres avancés__ dans le menu de gauche. Dans Paramètres avancés, sélectionnez __Sources connectées__>__Serveurs Linux__.


> [!IMPORTANT]
> Un délai de 20 minutes peut s’écouler avant que les données soient disponibles pour Log Analytics.

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

Pour plus d’informations sur Log Analytics, consultez le document [Prise en main d’un espace de travail Log Analytics](../../log-analytics/log-analytics-get-started.md).

Pour plus d’informations sur l’utilisation de Kafka, consultez les documents suivants :

 * [Mettre en miroir de Kafka entre des clusters HDInsight](apache-kafka-mirroring.md)
 * [Augmenter l’évolutivité de Kafka sur HDInsight](apache-kafka-scalability.md)
 * [Utiliser la diffusion en continu Spark (DStream) avec Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Utiliser Spark Structured Streaming avec Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
