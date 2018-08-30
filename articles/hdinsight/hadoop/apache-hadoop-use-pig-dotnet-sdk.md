---
title: Exécuter des travaux Apache Pig avec le SDK .NET pour Hadoop - Azure HDInsight
description: Apprenez à utiliser le Kit de développement logiciel (SDK) .NET pour Hadoop afin de soumettre des tâches Pig vers Hadoop sur HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: jasonh
ms.openlocfilehash: 06d2633556e149f48c9750b5df0408b601bf3da3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046758"
---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Exécution de tâches Pig à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Apprenez à utiliser le Kit de développement logiciel (SDK) .NET pour Hadoop afin de soumettre des travaux Pig vers Hadoop sur Azure HDInsight.

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation des clusters HDInsight à partir de .NET. Pig permet de créer des opérations MapReduce en modélisant une série de transformations de données. Dans ce document, vous apprenez à utiliser une application de base C# pour soumettre un travail Pig sur un cluster HDInsight.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants.

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux.

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio 2012, 2013, 2015 ou 2017.

## <a name="create-the-application"></a>Création de l’application

Le Kit de développement logiciel (SDK) HDInsight .NET fournit des bibliothèques clientes .NET, ce qui facilite l'utilisation des clusters HDInsight à partir de .NET.

1. Dans le menu **Fichier** de Visual Studio, sélectionnez **Nouveau**, puis **Projet**.

2. Pour le nouveau projet, tapez ou sélectionnez les valeurs suivantes :

   | Propriété | Valeur |
   | ------ | ------ |
   | Catégorie | Modèles/Visual C#/Windows |
   | Modèle | Application console |
   | NOM | SubmitPigJob |

3. Cliquez sur **OK** pour créer le projet.

4. À partir du menu **Outils**, sélectionnez **Gestionnaire de package de bibliothèque** ou **Gestionnaire de package NuGet**, puis sélectionnez **Console du gestionnaire de package**.

5. Pour installer les packages du Kit de développement logiciel (SDK) .NET, utilisez la commande suivante :

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir. Remplacez le code existant par ce qui suit.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
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

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Pour démarrer l’application, appuyez sur **F5**.

8. Pour quitter l’application, appuyez sur **Entrée**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Pig dans HDInsight, consultez [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md).

Pour plus d’informations sur l’utilisation de Hadoop sur HDInsight, consultez les documents suivants :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
