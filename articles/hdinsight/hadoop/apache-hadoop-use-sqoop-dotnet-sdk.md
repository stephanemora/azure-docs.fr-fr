---
title: Exécuter des tâches Apache Sqoop avec .NET et HDInsight - Azure
description: Découvrez comment utiliser le kit SDK .NET HDInsight pour exécuter l’exportation et l’importation Apache Sqoop entre un cluster Apache Hadoop et une base de données Azure SQL.
keywords: travail sqoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: ac0890be0abccb316bffc4d9bdd6868a80173e18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710116"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Exécuter des tâches Apache Sqoop avec le SDK .NET pour Apache Hadoop dans HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser le kit SDK .NET Azure HDInsight pour exécuter des tâches Apache Sqoop dans HDInsight afin d’effectuer des opérations d’importation et d’exportation entre un cluster HDInsight et une base de données Azure SQL ou SQL Server.

> [!NOTE]
> Même si vous pouvez utiliser les procédures décrites dans cet article avec un cluster HDInsight Windows ou Linux, ces dernières fonctionnent uniquement à partir d’un client Windows. Pour choisir d’autres méthodes, utilisez le sélecteur d’onglet en haut de cet article.

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel, vous devez disposer de l’élément suivant :

* Un cluster Apache Hadoop dans HDInsight. Pour plus d’informations, consultez [Création du cluster et de la base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Utiliser Sqoop sur des clusters HDInsight avec le Kit de développement logiciel (SDK) .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation des clusters HDInsight à partir de .NET. Dans cette section, vous allez créer une application console C# pour exporter la table hivesampletable vers la table Azure SQL Database créée précédemment dans ce tutoriel.

## <a name="submit-a-sqoop-job"></a>Envoyer un travail Sqoop

1. Créez une application console C# dans Visual Studio.

2. Dans la console du gestionnaire de package Visual Studio, importez le package en exécutant la commande NuGet suivante :
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Utilisez le code suivant dans le fichier Program.cs :
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Pour lancer le programme, appuyez sur la touche **F5**. 

## <a name="limitations"></a>Limites
HDInsight basé sur Linux présente les limitations suivantes :

* Exportation en bloc : le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou Azure SQL Database ne prend pas en charge actuellement les insertions en bloc.

* Traitement par lot : lorsque vous utilisez le commutateur `-batch`, Sqoop effectue plusieurs insertions au lieu de traiter par lot les opérations d’insertion.

## <a name="next-steps"></a>Étapes suivantes
Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utiliser Apache Oozie avec HDInsight](../hdinsight-use-oozie-linux-mac.md) : Utilisez l’action Sqoop dans un workflow Oozie.
* [Chargez des données dans HDInsight](../hdinsight-upload-data.md) : découvrez d’autres méthodes pour charger des données dans HDInsight ou le stockage Blob Azure.

