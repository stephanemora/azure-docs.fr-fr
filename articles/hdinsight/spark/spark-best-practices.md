---
title: Bonnes pratiques d’utilisation d’Apache Spark sur Azure HDInsight
description: Découvrez les bonnes pratiques d’utilisation d’Apache Spark sur Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106881"
---
# <a name="apache-spark-best-practices"></a>Bonnes pratiques d’utilisation d’Apache Spark

Cet article présente quelques-unes des bonnes pratiques à adopter pour utiliser Apache Spark sur Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Comment exécuter ou envoyer des travaux Spark ?

| Option | Documents |
|---|---|
| VSCode | [Utiliser les outils Spark et Hive pour Visual Studio Code](../hdinsight-for-vscode.md) |
| Notebooks Jupyter | [Tutoriel : Charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Tutoriel : Utiliser Azure Toolkit for IntelliJ pour créer des applications Apache Spark pour un cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Tutoriel : Créer une application Scala Maven pour Apache Spark dans HDInsight à l’aide d’IntelliJ](./apache-spark-create-standalone-application.md) |
| Notebooks Zeppelin | [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](./apache-spark-zeppelin-notebook.md) |
| Envoi de travaux à distance avec Livy | [Utiliser l’API REST Spark Apache pour envoyer des travaux à distance à un cluster Spark HDInsight](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Comment superviser et déboguer les travaux Spark ?

| Option | Documents |
|---|---|
| Kit de ressources Azure pour IntelliJ | [Échec du débogage du travail Spark avec Azure Toolkit for IntelliJ (préversion)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ via SSH | [Déboguer les applications Apache Spark localement ou à distance sur un cluster HDInsight, avec Azure Toolkit for IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ via VPN | [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance dans HDInsight via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Graphe du travail sur le serveur d’historique Apache Spark | [Utiliser le serveur d’historique Apache Spark étendu pour déboguer et diagnostiquer des applications Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Comment faire en sorte que mes travaux Spark s’exécutent plus efficacement ?

| Option | Documents |
|---|---|
| Cache d’E/S | [Améliorer les performances des charges de travail Apache Spark à l’aide d’Azure HDInsight IO Cache (préversion)](./apache-spark-improve-performance-iocache.md) |
| Options de configuration | [Optimiser des travaux Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Comment me connecter à d’autres services Azure ?

| Option | Documents |
|---|---|
| Apache Hive sur HDInsight | [Intégrer Apache Spark et Apache Hive au le connecteur d’entrepôt Hive](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase sur HDInsight | [Utiliser Apache Spark pour lire et écrire des données Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka sur HDInsight | [Tutoriel : Utiliser Apache Spark Structured Streaming avec Apache Kafka sur HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB : Implémenter une architecture lambda sur la plateforme Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Quelles sont mes options de stockage ?

| Option | Documents |
|---|---|
| Data Lake Storage Gen2 | [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen 1 | [Utiliser Data Lake Storage Gen1 avec des clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Stockage Blob Azure | [Utiliser Stockage Azure avec des clusters Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Étapes suivantes

* [Configurer les paramètres d’Apache Spark](apache-spark-settings.md)
* [Optimiser les travaux Apache Spark dans Azure HDInsight](apache-spark-perf.md)
