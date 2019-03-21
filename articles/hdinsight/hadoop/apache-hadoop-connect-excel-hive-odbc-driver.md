---
title: Connecter Excel à Apache Hadoop à l’aide du pilote ODBC Hive - Azure HDInsight
description: Découvrez comment configurer et utiliser le pilote ODBC Microsoft Hive pour Excel afin d’interroger des données dans des clusters HDInsight à partir de Microsoft Excel.
keywords: hadoop excel,hive excel,hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: hrasheed
ms.openlocfilehash: 3a47b18051036e925e54b9507bf2cb4e40aad844
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202520"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Connecter Excel à Apache Hadoop dans Azure HDInsight avec le pilote ODBC Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

La solution de données volumineuses de Microsoft intègre des composants BI (Business Intelligence) à des clusters Apache Hadoop qui ont été déployés dans Azure HDInsight. Un exemple de cette intégration est la possibilité de connecter Excel à l’entrepôt de données Hive d’un cluster Hadoop dans HDInsight au moyen du pilote Open Database Connectivity (ODBC) Microsoft Hive.

Il est également possible de connecter les données associées à un cluster HDInsight et d'autres sources de données, y compris d'autres clusters Hadoop (non HDInsight), à partir d'Excel au moyen du complément Microsoft Power Query pour Excel. Pour plus d’informations sur l’installation et l’utilisation de Power Query, consultez [Connexion d’Excel à HDInsight à l’aide de Power Query][hdinsight-power-query].


## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

* Un cluster HDInsight Hadoop. Pour en créer un, consultez [Prise en main d’Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Une station de travail avec Office Professionnel Plus 2010 ou version ultérieure, ou avec Excel 2010 ou version ultérieure.

## <a name="install-microsoft-hive-odbc-driver"></a>Installation du pilote ODBC Microsoft Hive
Téléchargez et installez la version du [pilote ODBC Microsoft Hive][hive-odbc-driver-download] qui correspond à celle de l’application dans laquelle vous utiliserez le pilote ODBC.  Pour les besoins de ce didacticiel, le pilote concerne Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Créer une source de données ODBC Apache Hive
La procédure suivante explique comment créer une source de données ODBC Hive.

1. Dans Windows, accédez à Démarrer > Outils d’administration Windows > Sources de données ODBC (32 bits)/(64 bits).  La fenêtre **Administrateur de sources de données ODBC** s’affiche.
   
    ![Administrateur de sources de données ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Configurer un DSN à l’aide de l’Administrateur de sources de données ODBC")

2. Dans l’onglet **DSN utilisateur**, sélectionnez **Ajouter** pour ouvrir la fenêtre **Créer une nouvelle source de données**.

3. Sélectionnez **Pilote ODBC Microsoft Hive**, puis **Terminer** pour ouvrir la fenêtre **Microsoft Hive ODBC Driver DSN Setup** (Configuration du DSN du pilote ODBC Microsoft Hive).

4. Tapez ou sélectionnez les valeurs suivantes :
   
   | Propriété | Description |
   | --- | --- |
   |  Data Source Name |Donnez un nom à votre source de données |
   |  Host(s) (Hôte(s)) |Entrez &lt;HDInsightClusterName&gt;.azurehdinsight.net. Par exemple, myHDICluster.azurehdinsight.net |
   |  Port |Utilisez <strong>443</strong>. (ce port est passé de 563 à 443). |
   |  Base de données |Utilisez <strong>Default</strong>. |
   |  Mechanism |Sélectionnez <strong>Azure HDInsight Service</strong>. |
   |  User Name |Entrez le nom de l’utilisateur HTTP du cluster HDInsight. Le nom d’utilisateur par défaut est <strong>admin</strong>. |
   |  Mot de passe |Entrez le mot de passe du cluster HDInsight. |

   
5. Facultatif : Sélectionnez **Options avancées...**  
   
   | Paramètre | Description |
   | --- | --- |
   |  Use Native Query |Une fois sélectionné, le pilote ODBC ne tente PAS de convertir TSQL en HiveQL. À utiliser uniquement si vous êtes sûr à 100 % que vous envoyez des instructions HiveQL pures. Si vous effectuez une connexion à SQL Server ou Base de données SQL Azure, ne sélectionnez pas cette option. |
   |  Rows fetched per block |Lors de l’extraction d’un grand nombre d’enregistrements, la définition de ce paramètre peut être nécessaire pour garantir des performances optimales. |
   |  Default string column length, Binary column length, Decimal column scale |Les précisions et longueurs des types de données peuvent affecter la façon dont les données sont renvoyées. Elles entraînent le renvoi d’informations incorrectes en raison d’une perte de précision et/ou de troncations. |

    ![Options avancées](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Options avancées de configuration de DSN")

5. Sélectionnez **Tester** pour tester la source de données. Une fois que la source de données est configurée correctement, le message suivant apparaît **SUCCESS!**.  

6. Sélectionnez **OK** pour fermer la fenêtre de test.  

7. Sélectionnez **OK** pour fermer la fenêtre **Microsoft Hive ODBC Driver DSN Setup** (Configuration du DSN du pilote ODBC Microsoft Hive).  

8. Sélectionnez **OK** pour fermer la fenêtre **Administrateur de sources de données ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importation de données dans Microsoft Excel à partir de HDInsight
Les étapes ci-dessous décrivent comment importer des données d’une table Hive dans un classeur Excel à l’aide de la source de données ODBC que vous avez créée dans la section précédente.

1. Ouvrez un nouveau classeur ou un classeur existant dans Excel.

2. À partir de l’onglet **Données**, accédez à **Obtenir des données** > **Depuis d’autres sources** > **Depuis ODBC** afin de lancer la fenêtre **Depuis ODBC**.
   
    ![Assistant Ouvrir la connexion de données](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Assistant Ouvrir la connexion de données")

3. Dans la liste déroulante, sélectionnez le nom de la source de données que vous avez créée dans la dernière section, puis sélectionnez **OK**.

4. Entrez le nom d’utilisateur Hadoop (le nom par défaut est admin) et le mot de passe, puis sélectionnez **Connecter** pour ouvrir la fenêtre **Navigateur**.

5. Dans la fenêtre **Navigateur**, accédez à **HIVE** > **default** > **hivesampletable**, puis sélectionnez **Charger**. Patientez quelques instants pour que les données soient importées dans Excel.

    ![Navigateur ODBC HDInsight Hive](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Assistant Ouvrir la connexion de données")

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser le pilote ODBC Microsoft Hive pour extraire des données du service HDInsight dans Excel. De la même façon, vous pouvez extraire les données du service HDInsight dans la base de données SQL. Il est également possible de télécharger des données dans un service HDInsight. Pour plus d'informations, consultez les rubriques suivantes :

* [Visualiser des données Apache Hive à l’aide de Microsoft Power BI dans Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connecter Excel à Apache Hadoop à l’aide de Power Query](apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Apache Hive avec Data Lake Tools pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](../hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


