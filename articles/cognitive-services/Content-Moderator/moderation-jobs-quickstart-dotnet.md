---
title: Azure Content Moderator - Lancer des tâches de modérations à l’aide de .NET | Microsoft Docs
description: Comment initier des tâches de modération à l’aide du kit de développement logiciel (SDK) Azure Content Moderator pour .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368049"
---
# <a name="start-moderation-jobs-using-net"></a>Lancer des tâches de modération à l’aide de .NET

Cet article fournit des informations et des exemples de code qui vont vous aider à démarrer avec le kit SDK Azure Content Moderator pour .NET pour :
 
- Lancez une tâche de modération pour analyser et créer des révisions pour des modérateurs humains
- Obtenir l’état de la révision en attente
- Suivre et obtenir l’état final de la révision
- Envoyer le résultat à l’URL de rappel

Cet article part du principe que vous connaissez déjà Visual Studio et C#.

## <a name="sign-up-for-content-moderator-services"></a>S’inscrire aux services Content Moderator

Avant de pouvoir utiliser les services Content Moderator par le biais de l’API REST ou du kit SDK, vous avez besoin d’une clé d’abonnement.
Reportez-vous au guide de [démarrage rapide](quick-start.md) pour découvrir comment obtenir la clé.

## <a name="define-a-custom-moderation-workflow"></a>Définir un workflow de modération personnalisé

Une tâche de modération analyse votre contenu à l’aide des API et utilise un **workflow** pour déterminer s’il faut créer des révisions ou non.
Bien que l’outil de révision contient un workflow par défaut, [définissons un workflow personnalisé](Review-Tool-User-Guide/Workflows.md) pour ce guide de démarrage rapide.

Vous utilisez le nom du workflow dans votre code qui démarre la tâche de modération.

## <a name="create-your-visual-studio-project"></a>Créer votre projet Visual Studio

1. Ajoutez un nouveau projet **Console app (.NET Framework)** à votre solution.

   Dans l’exemple de code, nommez le projet **CreateReviews**.

1. Sélectionnez ce projet comme unique projet de démarrage de la solution.

1. Ajoutez une référence à l’assembly de projet **ModeratorHelper** que vous avez créé dans le guide de [démarrage rapide d’assistance du client Content Moderator](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Installer les packages nécessaires

Installez les packages NuGet suivants :

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Mettre à jour les instructions using du programme

Mettez à jour les instructions d’utilisation du programme.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>Initialiser les paramètres spécifiques de l’application

Ajoutez les constantes et champs statiques suivants à la classe **Program** dans Program.cs.

> [!NOTE]
> Vous définissez la constante TeamName par le nom utilisé lorsque vous avez créé votre abonnement à Content Moderator. Vous récupérez la constante TeamName depuis le [site web Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Une fois connecté, sélectionnez **Informations d'identification** dans le menu (engrenage) **Paramètres**.
>
> Le nom de votre équipe est la valeur du champ **Id** dans la section **API**.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Ajouter du code pour modérer automatiquement, créer une révision et obtenir les détails d’une tâche

> [!Note]
> En pratique, vous définissez l’URL de rappel **CallbackEndpoint** comme URL qui recevra les résultats de la révision manuelle (via une requête HTTP POST).

Commencez par ajouter le code suivant à la méthode **Main**.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> Votre clé de service Content Moderator a une limite de fréquence des demandes par seconde (RPS). Si vous dépassez la limite, le kit SDK lève une exception avec le code d’erreur 429. 
>
> Une clé de niveau gratuit a une limite de fréquence d’une demande par seconde (RSP).

## <a name="run-the-program-and-review-the-output"></a>Exécuter le programme et examiner la sortie

L’exemple de sortie suivante s’affiche dans la console :

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Connectez-vous à l’outil de révision Content Moderator pour consulter la révision d’images en attente.

Utilisez le bouton **Suivant** pour envoyer.

![Révision d’images pour les modérateurs humains](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Consulter l’exemple de sortie dans le fichier journal

> [!NOTE]
> Dans votre fichier de sortie, les chaînes **Teamname**, **ContentId**, **CallBackEndpoint** et **WorkflowId** reflètent les valeurs que vous avez utilisées précédemment.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>Si fournie, votre URL de rappel reçoit cette réponse

Vous verrez une réponse semblable à celle-ci :

> [!NOTE]
> Dans votre réponse de rappel, les chaînes **ContentId** et **WorkflowId** reflètent les valeurs que vous avez utilisées précédemment.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>Étapes suivantes

[Téléchargez la solution Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pour ce guide de démarrage rapide et d’autres guides de démarrage rapide Content Moderator pour .NET, puis commencez votre intégration.
