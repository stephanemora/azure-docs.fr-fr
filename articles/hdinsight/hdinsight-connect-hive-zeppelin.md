---
title: Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight
description: Apprenez à utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive.
keywords: hdinsight,hadoop,hive,interactive query,LLAP
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 035e70eef88d5d5dae08c329017430db25c20464
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494823"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight 

Les clusters Interactive Query HDInsight incluent les blocs-notes [Apache Zeppelin](https://zeppelin.apache.org/) que vous pouvez utiliser pour exécuter des requêtes Hive interactives. Dans cet article, vous allez apprendre à utiliser Apache Zeppelin pour exécuter des requêtes [Apache Hive](https://hive.apache.org/) dans Azure HDInsight. 

## <a name="prerequisites"></a>Prérequis
Avant de poursuivre cet article, vérifiez que vous avez les éléments suivants :

* **Cluster Interactive Query HDInsight**. Consultez [Créer un cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) pour créer un cluster HDInsight.  Vous devez sélectionner le type Interactive Query. 

## <a name="create-an-apache-zeppelin-note"></a>Créer une note Apache Zeppelin

1. Accédez à l’URL suivante :

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Remplacez **CLUSTERNAME** par le nom de votre cluster.

2. Entrez votre nom d’utilisateur Hadoop et votre mot de passe. Dans la page Zeppelin, choisissez d’ouvrir une note existante ou d’en créer une. HiveSample contient plusieurs exemples de requêtes Hive.  

    ![Zeppelin avec Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Cliquez sur **Create new Note**.
4. Tapez ou sélectionnez les valeurs suivantes :

    - Note name : entrez un nom pour la note.
    - Default interpreter : sélectionnez **JDBC**.

5. Cliquez sur **Create Note**.
6. Exécutez la requête Hive suivante :

        %jdbc(hive)
        show tables

    ![exécution de la requête Interactive Query HDInsight avec Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    L’instruction **%jdbc(hive)** dans la première ligne indique au notebook d’utiliser l’interpréteur Hive JDBC.

    La requête retourne une table Hive appelée *hivesampletable*.

    Vous pouvez exécuter les deux autres requêtes Hive suivantes sur la table hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Les résultats de la requête sont retournés plus rapidement qu’avec une requête Hive standard.


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à visualiser des données HDInsight à l’aide de [Microsoft Power BI](https://powerbi.microsoft.com/).  Pour en savoir plus, consultez les articles suivants :

* [Visualiser des données Apache Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualiser des données Interactive Query Apache Hive à l’aide de Power BI dans Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connecter Excel à Apache Hadoop à l’aide de Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Apache Hive avec Data Lake Tools pour Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./hdinsight-upload-data.md).
