---
title: Excel et Apache Hadoop avec le Pilote ODBC – Azure HDInsight
description: Découvrez comment configurer et utiliser le pilote ODBC Microsoft Hive pour Excel afin d’interroger des données dans des clusters HDInsight à partir de Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/11/2019
ms.openlocfilehash: 883192e1d041014c23445b7a2fa0ece45eb76f10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435809"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Connecter Excel à Apache Hadoop dans Azure HDInsight avec le pilote ODBC Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

La solution de données volumineuses de Microsoft intègre des composants BI (Business Intelligence) à des clusters Apache Hadoop qui ont été déployés dans Azure HDInsight. Un exemple de cette intégration est la possibilité de connecter Excel à l’entrepôt de données Hive d’un cluster Hadoop dans HDInsight au moyen du pilote Open Database Connectivity (ODBC) Microsoft Hive.

Il est également possible de connecter les données associées à un cluster HDInsight et d'autres sources de données, y compris d'autres clusters Hadoop (non HDInsight), à partir d'Excel au moyen du complément Microsoft Power Query pour Excel. Pour plus d’informations sur l’installation et l’utilisation de Power Query, consultez [Connexion d’Excel à HDInsight à l’aide de Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer cet article, vous devez disposer des éléments suivants :

* Un cluster HDInsight Hadoop. Pour en créer un, consultez [Prise en main d’Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Une station de travail avec Office Professionnel Plus 2010 ou version ultérieure, ou avec Excel 2010 ou version ultérieure.

## <a name="install-microsoft-hive-odbc-driver"></a>Installation du pilote ODBC Microsoft Hive

Téléchargez et installez la version du [pilote ODBC Microsoft Hive](https://www.microsoft.com/download/details.aspx?id=40886) qui correspond à celle de l’application dans laquelle vous utiliserez le pilote ODBC.  Pour les besoins de cet article, le pilote concerne Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Créer une source de données ODBC Apache Hive

La procédure suivante explique comment créer une source de données ODBC Hive.

1. Dans Windows, accédez à Démarrer > Outils d’administration Windows > Sources de données ODBC (32 bits)/(64 bits).  La fenêtre **Administrateur de sources de données ODBC** s’affiche.

    ![Administrateur de sources de données ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configurer un DSN à l’aide de l’Administrateur de sources de données ODBC")

1. Dans l’onglet **DSN utilisateur**, sélectionnez **Ajouter** pour ouvrir la fenêtre **Créer une nouvelle source de données**.

1. Sélectionnez **Pilote ODBC Microsoft Hive**, puis **Terminer** pour ouvrir la fenêtre **Microsoft Hive ODBC Driver DSN Setup** (Configuration du DSN du pilote ODBC Microsoft Hive).

1. Tapez ou sélectionnez les valeurs suivantes :

   | Propriété | Description |
   | --- | --- |
   |  Nom de la source de données |Donnez un nom à votre source de données |
   |  Host(s) (Hôte(s)) |Entrez `HDInsightClusterName.azurehdinsight.net`. Par exemple : `myHDICluster.azurehdinsight.net`. Remarque : `HDInsightClusterName-int.azurehdinsight.net` est pris en charge tant que la machine virtuelle cliente est appairée au même réseau virtuel. |
   |  Port |Utilisez **443**. (ce port est passé de 563 à 443). |
   |  Base de données |Utilisez **Default**. |
   |  Mechanism |Sélectionnez **Windows Azure HDInsight Service**. |
   |  User Name |Entrez le nom de l’utilisateur HTTP du cluster HDInsight. Le nom d’utilisateur par défaut est **admin**. |
   |  Mot de passe |Entrez le mot de passe du cluster HDInsight. Cochez la case **Enregistrer le mot de passe (chiffré)** .|

1. Facultatif : Sélectionnez **Options avancées...**  

   | Paramètre | Description |
   | --- | --- |
   |  Use Native Query |Une fois sélectionné, le pilote ODBC ne tente PAS de convertir TSQL en HiveQL. À utiliser uniquement si vous êtes sûr à 100 % que vous envoyez des instructions HiveQL pures. Si vous effectuez une connexion à SQL Server ou Azure SQL Database, ne sélectionnez pas cette option. |
   |  Rows fetched per block |Lors de l’extraction d’un grand nombre d’enregistrements, la définition de ce paramètre peut être nécessaire pour garantir des performances optimales. |
   |  Default string column length, Binary column length, Decimal column scale |Les précisions et longueurs des types de données peuvent affecter la façon dont les données sont renvoyées. Elles entraînent le renvoi d’informations incorrectes en raison d’une perte de précision et/ou de troncations. |

    ![Options avancées de configuration DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Options avancées de configuration DSN")

1. Sélectionnez **Tester** pour tester la source de données. Une fois que la source de données est configurée correctement, le message suivant apparaît **SUCCESS!** .  

1. Sélectionnez **OK** pour fermer la fenêtre de test.  

1. Sélectionnez **OK** pour fermer la fenêtre **Microsoft Hive ODBC Driver DSN Setup** (Configuration du DSN du pilote ODBC Microsoft Hive).  

1. Sélectionnez **OK** pour fermer la fenêtre **Administrateur de sources de données ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importation de données dans Microsoft Excel à partir de HDInsight

Les étapes ci-dessous décrivent comment importer des données d’une table Hive dans un classeur Excel à l’aide de la source de données ODBC que vous avez créée dans la section précédente.

1. Ouvrez un nouveau classeur ou un classeur existant dans Excel.

2. À partir de l’onglet **Données**, accédez à **Obtenir des données** > **Depuis d’autres sources** > **Depuis ODBC** afin de lancer la fenêtre **Depuis ODBC**.

    ![Assistant Ouvrir la connexion de données Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Assistant Ouvrir la connexion de données Excel")

3. Dans la liste déroulante, sélectionnez le nom de la source de données que vous avez créée dans la dernière section, puis sélectionnez **OK**.

4. Lors de la première utilisation, une boîte de dialogue **Pilote ODBC** s’ouvre. Dans le menu gauche, sélectionnez **Windows**. Sélectionnez ensuite **Se connecter** pour ouvrir la fenêtre **Navigateur**.

5. Dans la fenêtre **Navigateur**, accédez à **HIVE** > **default** > **hivesampletable**, puis sélectionnez **Charger**. Patientez quelques instants pour que les données soient importées dans Excel.

    ![Navigateur ODBC Hive HDInsight pour Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Navigateur ODBC Hive HDInsight pour Excel")

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser le pilote ODBC Microsoft Hive pour extraire des données du service HDInsight dans Excel. De la même façon, vous pouvez extraire les données du service HDInsight dans la base de données SQL. Il est également possible de télécharger des données dans un service HDInsight. Pour plus d'informations, consultez les rubriques suivantes :

* [Visualiser des données Apache Hive à l’aide de Microsoft Power BI dans Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connecter Excel à Apache Hadoop à l’aide de Power Query](apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Apache Hive avec Data Lake Tools pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](../hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./../hdinsight-upload-data.md).
