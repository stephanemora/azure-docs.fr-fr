---
title: Exécuter des requêtes Apache Hive avec le Kit de développement logiciel (SDK) .NET HDInsight - Azure
description: Découvrez comment envoyer des travaux Apache Hadoop à Azure HDInsight Apache Hadoop à l’aide du Kit de développement logiciel (SDK) .NET HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: cdb2207ab834ab84cedd2ad23e5e26186ac44039
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020471"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Exécuter des requêtes Apache Hive avec le Kit de développement logiciel (SDK) .NET HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Découvrez comment envoyer des requêtes Apache Hive avec le Kit de développement logiciel (SDK) .NET HDInsight. Vous écrivez un programme C# pour soumettre une requête Hive destinée à répertorier les tables Hive, et vous affichez les résultats.

> [!NOTE]  
> Les étapes décrites dans cet article doivent être effectuées à partir d'un client Windows. Pour plus d’informations sur l’utilisation d’un client Linux, OS X ou Unix pour utiliser Hive, utilisez le sélecteur d’onglet en haut de l’article.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer cet article, vous devez disposer des éléments suivants :

* Un cluster Apache Hadoop dans HDInsight. Consultez [Prise en main de Hadoop sous Linux dans HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > À compter du 15 septembre 2017, le kit HDInsight .NET SDK prend uniquement en charge le retour des résultats de requête Hive provenant des comptes de stockage Azure. Si vous utilisez cet exemple avec un cluster HDInsight qui utilise Azure Data Lake Storage en tant que stockage principal, vous ne pouvez pas récupérer les résultats de recherche avec le kit .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 et versions ultérieures. Vous devez installer au moins la charge de travail **développement de Bureau .NET**.

## <a name="run-a-hive-query"></a>Exécuter une requête Hive

Le Kit de développement logiciel (SDK) HDInsight .NET fournit des bibliothèques clientes .NET, ce qui facilite l'utilisation des clusters HDInsight à partir de .NET.

1. Créez une application console C# dans Visual Studio.

1. À partir de la console du gestionnaire de package NuGet, exécutez la commande suivante :

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Modifiez le code ci-dessous pour initialiser les valeurs des variables : `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`. Utilisez ensuite le code modifié comme contenu complet de **Program.cs** dans Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Appuyez sur **F5** pour exécuter l'application.

Le résultat de l’application devrait être similaire à :

![Résultat de la tâche HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment envoyer des requêtes Apache Hive avec le Kit de développement logiciel (SDK) .NET HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d’Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Créer des clusters Apache Hadoop dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Référence du Kit de développement logiciel (SDK) .NET de HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Utiliser Apache Sqoop avec HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Créer des applications .NET HDInsight d’authentification non interactives](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
