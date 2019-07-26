---
title: Apache Sqoop avec Apache Hadoop - Azure HDInsight
description: Apprenez à utiliser Apache Sqoop pour échanger des données entre Apache Hadoop sur HDInsight et une base de données Azure SQL Database.
keywords: hadoop sqoop, sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406048"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Utiliser Apache Sqoop pour échanger des données entre Apache Hadoop sur HDInsight et SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Apprenez à utiliser Apache Sqoop pour échanger des données entre un cluster Apache Hadoop dans Azure HDInsight et une base de données Azure SQL Database ou Microsoft SQL Server. Les étapes décrites dans ce document utilisent la commande `sqoop` directement à partir du nœud principal du cluster Hadoop. Vous utilisez SSH pour vous connecter au nœud principal et exécutez les commandes décrites dans ce document. Cet article est la suite de [Utiliser Apache Sqoop avec Hadoop dans HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Prérequis

* Avoir effectué [Configurer un environnement de test](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) dans [Utiliser Apache Sqoop avec Hadoop dans HDInsight](./hdinsight-use-sqoop.md).

* Un client pour interroger la base de données Azure SQL. Envisagez d’utiliser [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) ou [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Un client SSH. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Exportation de Sqoop

De Hive vers SQL Server.

1. Utilisez SSH pour vous connecter au cluster HDInsight. Remplacez `CLUSTERNAME` par le nom de votre cluster, puis entrez la commande :

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Remplacez `MYSQLSERVER` par le nom de votre serveur SQL Server. Pour vérifier que Sqoop peut voir votre base de données SQL Database, entrez la commande ci-dessous dans votre connexion SSH ouverte. Entrez le mot de passe de la connexion SQL Server quand vous y êtes invité. Cette commande retourne une liste de bases de données.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Remplacez `MYSQLSERVER` par le nom de votre serveur SQL Server, et `MYDATABASE` par le nom de votre base de données SQL. Pour exporter des données depuis la table Hive `hivesampletable` vers la table `mobiledata` dans SQL Database, entrez la commande ci-dessous dans votre connexion SSH ouverte. Entrez le mot de passe pour la connexion SQL Server quand vous y êtes invité

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Pour vérifier que les données ont été exportées, utilisez les requêtes suivantes à partir de votre client SQL pour voir les données exportées :

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Importation de Sqoop

Depuis SQL Server vers Stockage Azure.

1. Remplacez `MYSQLSERVER` par le nom de votre serveur SQL Server, et `MYDATABASE` par le nom de votre base de données SQL. Entrez la commande ci-dessous dans votre connexion SSH ouverte pour importer des données depuis la table `mobiledata` dans SQL Database vers le répertoire `wasb:///tutorials/usesqoop/importeddata` sur HDInsight. Entrez le mot de passe de la connexion SQL Server quand vous y êtes invité. Les champs dans les données sont séparés par un caractère de tabulation et les lignes se terminent par un caractère de nouvelle ligne.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Une fois l’importation terminée, entrez la commande suivante dans votre connexion SSH ouverte pour lister les données dans le nouveau répertoire :

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Limites

* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou Azure SQL Database ne prend pas en charge les insertions en bloc.

* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="important-considerations"></a>Points importants à prendre en compte

* HDInsight et SQL Server doivent figurer sur le même réseau Azure Virtual Network.

    Pour voir un exemple, consultez le document [Connecter HDInsight à votre réseau local](./../connect-on-premises-network.md).

    Pour plus d’informations sur l’utilisation de HDInsight avec Azure Virtual Network, voir le document [Étendre HDInsight avec Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md). Pour plus d’informations sur Azure Virtual Network, voir [Présentation du réseau virtuel](../../virtual-network/virtual-networks-overview.md).

* Le serveur SQL Server doit également être configuré pour autoriser l’authentification SQL. Pour plus d’informations, consultez le document [Choisir un mode d’authentification](https://msdn.microsoft.com/ms144284.aspx).

* Vous devez configurer SQL Server pour qu’il accepte les connexions à distance. Pour plus d’informations, consultez le document [Résolution des problèmes de connexion au moteur de base de données SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utiliser Apache Oozie avec HDInsight](../hdinsight-use-oozie-linux-mac.md) : Utilisez l’action Sqoop dans un workflow Oozie.
* [Analyser des données sur les retards de vol avec HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md) : Utilisez Interactive Query pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données Azure SQL.
* [Chargez des données dans HDInsight](../hdinsight-upload-data.md) : Découvrez d’autres méthodes pour charger des données dans HDInsight ou le stockage Blob Azure.
