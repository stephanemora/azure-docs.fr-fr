---
title: Qu'est-ce qu'Interactive Query dans Azure HDInsight ?
description: Introduction à Interactive Query, également appelé Apache Hive LLAP, dans Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 09/17/2019
ms.openlocfilehash: f03797a8c7df1609a32f934bc090c7adc899aa9a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198920"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Qu'est-ce qu'Interactive Query dans Azure HDInsight

Le cluster Interactive Query (également appelé Apache Hive LLAP ou [Low Latency Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) est un [type de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type) Azure HDInsight. Interactive Query prend en charge la mise en mémoire cache, ce qui accélère les requêtes Apache Hive et les rend beaucoup plus interactives. Les clients utilisent Interactive Query pour interroger des données stockées dans le stockage Azure et Azure Data Lake Storage de manière extrêmement rapide. Une requête interactive permet facilement aux développeurs et scientifiques des données de travailler avec des données volumineuses (big data) à l’aide des outils décisionnels qu’ils préfèrent. HDInsight Interactive Query prend en charge plusieurs outils pour accéder aux données volumineuses de façon facile.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Les clusters Interactive Query sont différents des clusters Apache Hadoop. Ils contiennent uniquement le service Hive.

Vous pouvez accéder au service Hive dans le cluster Interactive Query uniquement par le biais de la vue Apache Ambari Hive, de Beeline et du pilote ODBC Microsoft Hive. Vous ne pouvez pas y accéder par le biais de la console Hive, Templeton, l’interface Azure Classic CLI, ni Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Créer un cluster Interactive Query

Pour obtenir des informations sur la création d’un cluster HDInsight, consultez [Créer des clusters Apache Hadoop dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Choisissez le type de cluster Interactive Query.

> [!IMPORTANT]
> La taille minimale du nœud principal pour les clusters Interactive Query est Standard_D13_v2. Pour plus d’informations, consultez le [Tableau de dimensionnement des machines virtuelles Azure](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Exécuter des requêtes Apache Hive à partir du cluster Interactive Query

Pour exécuter des requêtes Hive, vous disposez des options suivantes :

* Utiliser Microsoft Power BI

    Consultez [Visualiser des données Interactive Query Apache Hive à l’aide de Power BI dans Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md). Consultez [Visualiser des données volumineuses (« Big Data ») avec Power BI dans Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Utiliser Visual Studio

    Consultez [Se connecter à Azure HDInsight et exécuter des requêtes Apache Hive avec Data Lake Tools pour Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Utiliser Visual Studio Code

    Consultez [Utiliser Visual Studio Code pour Apache Hive, LLAP ou pySpark](../hdinsight-for-vscode.md).
* Exécutez Apache Hive à l’aide de la vue Ambari Apache Hive.
  
    Consultez [Utiliser la vue Apache Hive avec Apache Hadoop dans Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Exécuter Apache Hive à l’aide de Beeline
  
    Consultez [Utiliser Apache Hive avec Apache Hadoop dans HDInsight via Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Vous pouvez utiliser Beeline à partir du nœud principal ou d’un nœud de périphérie vide. L’utilisation de Beeline à partir d’un nœud de périphérie vide est recommandée. Pour plus d’informations sur la création d’un cluster HDInsight avec un nœud de périphérie vide, consultez [Utiliser des nœuds de périphérie vides dans HDInsight](../hdinsight-apps-use-edge-node.md).
* Exécuter Apache Hive à l’aide du pilote ODBC Hive
  
    Consultez [Connexion d’Excel à Apache Hadoop à l’aide du pilote ODBC Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Pour rechercher la chaîne de connexion Java Database Connectivity (JDBC) :

1. Connectez-vous à Apache Ambari à l’aide de l’URL suivante : `https://<cluster name>.AzureHDInsight.net`.
2. Dans le menu de gauche, sélectionnez **Hive**.
3. Pour copier l’URL, sélectionnez l’icône du Presse-papiers :

   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer des clusters Interactive Query dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Découvrez comment [visualiser des Big Data à l’aide de Power BI dans Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Découvrez comment [utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
