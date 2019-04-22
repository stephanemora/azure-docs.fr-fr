---
title: Copier des données entre Azure Data Lake Storage Gen1 et une base de données SQL Azure à l’aide de Sqoop | Microsoft Docs
description: Utiliser Sqoop pour copier des données entre Azure SQL Database et Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 7d3283b03d15278d1f7fd42a72b154dab1a442b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878522"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Copier des données entre Azure Data Lake Storage Gen1 et une base de données SQL Azure à l’aide de Sqoop
Découvrez comment utiliser Apache Sqoop pour importer et exporter des données entre Azure SQL Database et Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Qu'est-ce que Sqoop ?
Les applications Big Data sont un choix naturel pour le traitement des données non structurées et semi-structurées, telles que les journaux d’activité et les fichiers. Toutefois, il peut également être nécessaire de traiter des données structurées stockées dans des bases de données relationnelles.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) est un outil conçu pour transférer des données entre des bases de données relationnelles et un référentiel Big Data, par exemple Azure Data Lake Storage Gen1. Vous pouvez l’utiliser pour importer des données à partir d’un système de gestion de base de données relationnelle (SGBDR), comme Azure SQL Database, dans Azure Data Lake Storage Gen1. Vous pouvez ensuite transformer et analyser les données à l’aide de charges de travail Big Data, puis exporter les données dans un SGBDR. Dans ce didacticiel, vous utilisez une base de données SQL Azure comme base de données relationnelle pour les opérations d’importation et d’exportation.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Azure Data Lake Storage Gen1**. Pour savoir comment en créer un, consultez [Prise en main d’Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** avec accès à un compte Data Lake Storage Gen1. Voir [Créer un cluster HDInsight avec Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Cet article suppose que vous disposez d’un cluster Linux HDInsight avec accès à Azure Data Lake Storage Gen1.
* **Base de données SQL Azure**. Pour savoir comment en créer un, consultez [Créer une base de données SQL Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Les vidéos vous permettent-elles d’apprendre rapidement ?
[Regardez cette vidéo](https://mix.office.com/watch/1butcdjxmu114) pour savoir comment copier des données entre des objets blob Azure Storage et Data Lake Storage Gen1 à l’aide de DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Créer des exemples de tables dans la base de données SQL Azure
1. Pour commencer, créez deux exemples de tables dans la base de données SQL Azure. Utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio pour vous connecter à la base de données SQL Azure, puis exécutez les requêtes suivantes.

    **Créer Table1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Créer Table2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. Dans **Table1**, ajoutez des exemples de données. Laissez **Table2** vide. Nous importerons des données de **Table1** dans Azure Data Lake Storage Gen1. Ensuite, nous exporterons les données de Azure Data Lake Storage Gen1 vers **Table2**. Exécutez l'extrait de code suivant.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Utiliser Sqoop à partir d’un cluster HDInsight avec accès à Azure Data Lake Storage Gen1
Un cluster HDInsight dispose déjà des packages Sqoop. Si vous avez configuré le cluster HDInsight pour utiliser Data Lake Storage Gen1 comme un espace de stockage supplémentaire, vous pouvez utiliser Sqoop (sans aucune modification de configuration) pour importer/exporter des données entre une base de données relationnelle (dans cet exemple, Azure SQL Database) et un compte Data Lake Storage Gen1.

1. Pour ce didacticiel, nous supposons que vous avez créé un cluster Linux ; vous devez donc utiliser SSH pour vous connecter au cluster. Consultez [Connexion à un cluster HDInsight sous Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Vérifiez si vous pouvez accéder au compte Data Lake Storage Gen1 à partir du cluster. Exécutez la commande suivante depuis une invite SSH :

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    Vous devriez avoir la liste des fichiers/dossiers du compte Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importer des données à partir d’Azure SQL Database dans Data Lake Storage Gen1
1. Accédez au répertoire dans lequel se trouvent les packages Sqoop. En règle générale, il s’agit de `/usr/hdp/<version>/sqoop/bin`.
2. Importez les données de **Table1** dans le compte Data Lake Storage Gen1. Utilisez la syntaxe suivante :

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Notez que l’espace réservé **sql-database-server-name** représente le nom du serveur sur lequel s’exécute la base de données SQL Azure. L’espace réservé **sql-database-name** désigne le nom de la base de données.

    Par exemple,


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Vérifiez que les données ont été transférées vers le compte Data Lake Storage Gen1. Exécutez la commande suivante :

        hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    La sortie suivante s'affiche.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Chaque fichier **part-m-*** correspond à une ligne dans la table source **Table1**. Vous pouvez afficher le contenu des fichiers part-m-* à vérifier.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Exporter les données de Data Lake Storage Gen1 dans la base de données SQL Azure
1. Exportez les données du compte Data Lake Storage Gen1 vers la table vide, **Table2**, dans la base de données SQL Azure. Utilisez la syntaxe suivante.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Par exemple,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Vérifiez que les données ont été chargées sur la table de la base de données SQL. Utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio pour vous connecter à la base de données SQL Azure, puis exécutez la requête suivante.

        SELECT * FROM TABLE2

    Vous devriez obtenir le résultat suivant.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considérations de performances lors de l’utilisation de Sqoop

Pour optimiser les performances de votre tâche Sqoop pour copier des données vers Data Lake Storage Gen1, consultez le [document de performance Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Voir aussi
* [Copier des données d’objets blob Stockage Azure vers Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Sécuriser les données dans Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
