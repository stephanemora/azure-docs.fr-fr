---
title: Azure Content Moderator - Créer des révisions de transcriptions de vidéos à l’aide de .NET | Microsoft Docs
description: Comment créer des révisions de transcriptions de vidéos à l’aide du Kit de développement logiciel (SDK) Azure pour .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 3286da6e38f0fba02386d877a835fb694ed0fdec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "35368553"
---
# <a name="create-video-transcript-reviews-using-net"></a>Révisions de transcriptions de vidéos à l’aide de .NET

Cet article fournit des informations et des exemples de code pour vous aider à prendre rapidement en main le Kit de développement logiciel (SDK) Azure Content Moderator avec C# pour :

- Créer une révision de vidéo pour des modérateurs humains
- Ajouter une transcription modérée à la révision
- Publier la révision

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez [modéré la vidéo](video-moderation-api.md) et [créé la révision de la vidéo](video-reviews-quickstart-dotnet.md) dans l’outil de révision pour la prise de décision humaine. Vous souhaitez maintenant ajouter les transcriptions de la vidéo modérée dans l’outil de révision.

Cet article part également du principe que vous connaissez déjà Visual Studio et C#.

### <a name="sign-up-for-content-moderator-services"></a>S’inscrire aux services Content Moderator

Avant de pouvoir utiliser les services Content Moderator par le biais de l’API REST ou du Kit de développement logiciel (SDK), vous avez besoin d’une clé d’abonnement.

Dans le tableau de bord Content Moderator, vous trouvez la clé d’abonnement dans **Paramètres** > **Informations d’identification** > **API** > **Trial Ocp-Apim-Subscription-Key** (Essai Ocp-Apim-Subscription-Key) Pour plus d’informations, consultez [What is Content Moderator?](overview.md) (Présentation de Content Moderator).

### <a name="prepare-your-video-for-review"></a>Préparer votre vidéo pour la révision

Ajoutez la transcription à une révision de la vidéo. La vidéo doit être publiée en ligne. Vous avez besoin de son point de terminaison de streaming. Le point de terminaison de streaming permet au lecteur vidéo de l’outil de révision de lire la vidéo.

![Miniature de démonstration de vidéo](images/ams-video-demo-view.PNG)

- Copiez **l’URL** sur cette page de [démonstration Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) pour l’URL du manifeste.

## <a name="create-your-visual-studio-project"></a>Créer votre projet Visual Studio

1. Ajoutez un nouveau projet **Application console (.NET Framework)** à votre solution.

1. Nommez le projet **VideoTranscriptReviews**.

1. Sélectionnez ce projet en tant que projet de démarrage unique pour la solution.

### <a name="install-required-packages"></a>Installer les packages nécessaires

Installez les packages NuGet suivants pour le projet TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Mettre à jour les instructions using du programme

Modifiez les instructions using du programme comme suit.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Ajouter des propriétés privées

Ajoutez les propriétés privées suivantes à l’espace de noms VideoTranscriptReview de la classe Program.

Aux endroits indiqués, remplacez les exemples de valeurs pour ces propriétés.


    namespace VideoReviews
    {
        class Program
        {
            // NOTE: Replace this example location with the location for your Content Moderator account.
            /// <summary>
            /// The region/location for your Content Moderator account, 
            /// for example, westus.
            /// </summary>
            private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

            // NOTE: Replace this example key with a valid subscription key.
            /// <summary>
            /// Your Content Moderator subscription key.
            /// </summary>
            private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

            // NOTE: Replace this example team name with your Content Moderator team name.
            /// <summary>
            /// The name of the team to assign the job to.
            /// </summary>
            /// <remarks>This must be the team name you used to create your 
            /// Content Moderator account. You can retrieve your team name from
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

            /// <summary>
            /// The base URL fragment for Content Moderator calls.
            /// </summary>
            private static readonly string AzureBaseURL =
                $"{AzureRegion}.api.cognitive.microsoft.com";

            /// <summary>
            /// The minimum amount of time, in milliseconds, to wait between calls
            /// to the Content Moderator APIs.
            /// </summary>
            private const int throttleRate = 2000;


### <a name="create-content-moderator-client-object"></a>Créer un objet de client Content Moderator

Ajoutez la définition de méthode suivante à l’espace de noms VideoTranscriptReview de la classe Program.

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
        {
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Créer une révision de vidéo

Créez une révision de vidéo avec **ContentModeratorClient.Reviews.CreateVideoReviews**. Pour plus d’informations, consultez la [référence de l’API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** a les paramètres requis suivants :
1. Une chaîne qui contient un type MIME, qui doit être « application/json ». 
1. Le nom de votre équipe Content Moderator.
1. Un objet **IList<CreateVideoReviewsBodyItem>**. Chaque objet **CreateVideoReviewsBodyItem** représente une révision de vidéo. Ce démarrage rapide crée une révision à la fois.

**CreateVideoReviewsBodyItem** a plusieurs propriétés. Vous définissez au moins les propriétés suivantes :
- **Contenu**. L’URL de la vidéo à réviser.
- **ContentId**. Un ID à affecter à la révision de vidéo.
- **État**. Définissez la valeur sur « Non publié ». Si vous ne définissez pas cette valeur, elle sera par défaut définie sur « En attente », ce qui signifie que la révision de la vidéo est publiée et en attente d’une révision humaine. Une fois qu’une révision de vidéo est publiée, vous ne pouvez plus y ajouter de trames vidéo, de transcriptions ou de résultats de modération de transcription.

> [!NOTE]
> **CreateVideoReviews** renvoie une IList<string>. Chacune de ces chaînes contient un ID pour une révision de vidéo. Ces ID sont des GUID et ne sont pas les mêmes que la valeur de la propriété **ContentId**. 

Ajoutez la définition de méthode suivante à l’espace de noms VideoReviews de la classe Program.

    /// <summary>
    /// Create a video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="id">The ID to assign to the video review.</param>
    /// <param name="content">The URL of the video to review.</param>
    /// <returns>The ID of the video review.</returns>
    private static string CreateReview(ContentModeratorClient client, string id, string content)
    {
        Console.WriteLine("Creating a video review.");

        List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
            new CreateVideoReviewsBodyItem
            {
                Content = content,
                ContentId = id,
                /* Note: to create a published review, set the Status to "Pending".
                However, you cannot add video frames or a transcript to a published review. */
                Status = "Unpublished",
            }
        };

        var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

        Thread.Sleep(throttleRate);

        // We created only one review.
        return result[0];
    }

> [!NOTE]
> Votre clé de service Content Moderator a une limite de fréquence de requêtes par seconde. Si vous dépassez la limite, le Kit de développement logiciel (SDK) génère une exception avec un code d’erreur 429. 
>
> Une clé de niveau gratuit a une limite de fréquence d’une requête par seconde.

## <a name="add-transcript-to-video-review"></a>Ajouter la transcription à la révision de la vidéo

Vous ajoutez une transcription à une révision vidéo avec **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** a les paramètres requis suivants :
1. L’ID de votre équipe Content Moderator.
1. L’ID de révision de vidéo renvoyé par **CreateVideoReviews**.
1. Un objet **Flux** qui contient la transcription.

La transcription doit être au format WebVTT. Pour plus d’informations, consultez [WebVTT: The Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/) (WebVTT : le format Web Video Text Tracks).

> [!NOTE]
> Le programme utilise un exemple de transcription au format VTT. Dans une solution réelle, vous utilisez le service Azure Media Indexer pour [générer une transcription](https://docs.microsoft.com/azure/media-services/media-services-index-content) à partir d’une vidéo.

Ajoutez la définition de méthode suivante à l’espace de noms VideoTranscriptReview de la classe Program.

    /// <summary>
    /// Add a transcript to the indicated video review.
    /// The transcript must be in the WebVTT format.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
        client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Ajouter un résultat de modération de transcription à une révision de vidéo

En plus d’ajouter une transcription à une révision de vidéo, vous pouvez également ajouter le résultat de la modération de cette transcription. Vous pouvez le faire avec **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Pour plus d’informations, consultez la [référence de l’API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** a les paramètres requis suivants :
1. Une chaîne qui contient un type MIME, qui doit être « application/json ». 
1. Le nom de votre équipe Content Moderator.
1. L’ID de révision de vidéo renvoyé par **CreateVideoReviews**.
1. Une IList<TranscriptModerationBodyItem>. Un élément **TranscriptModerationBodyItem** a les propriétés suivantes :
- **Termes** Une IList<TranscriptModerationBodyItemTermsItem>. Un élément **TranscriptModerationBodyItemTermsItem** a les propriétés suivantes :
- **Index**. L’index de base zéro du terme.
- **Terme**. Une chaîne qui contient le terme.
- **Horodatage** Une chaîne qui contient le moment (exprimé en secondes) où se trouvent les termes dans la transcription.

La transcription doit être au format WebVTT. Pour plus d’informations, consultez [WebVTT: The Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/) (WebVTT : le format Web Video Text Tracks).

Ajoutez la définition de méthode suivante à l’espace de noms VideoTranscriptReview de la classe Program. Cette méthode envoie une transcription à la méthode **ContentModeratorClient.TextModeration.ScreenText**. Elle traduit également le résultat en une IList<TranscriptModerationBodyItem> et l’envoie à **AddVideoTranscriptModerationResult**.

    /// <summary>
    /// Add the results of moderating a video transcript to the indicated video review.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

        // Screen the transcript using the Text Moderation API. For more information, see:
        // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
        Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

        // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
        List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
        if (null != screen.Terms)
        {
            foreach (var term in screen.Terms)
            {
                if (term.Index.HasValue)
                {
                    terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
                }
            }
        }

        List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
        {
            new TranscriptModerationBodyItem()
            {
                Timestamp = "0",
                Terms = terms
            }
        };

        client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

        Thread.Sleep(throttleRate);
    }


## <a name="publish-video-review"></a>Publier la révision de vidéo

Vous publiez une révision de vidéo avec **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** a les paramètres requis suivants :
1. Le nom de votre équipe Content Moderator.
1. L’ID de révision de vidéo renvoyé par **CreateVideoReviews**.

Ajoutez la définition de méthode suivante à l’espace de noms VideoReviews de la classe Program.

    /// <summary>
    /// Publish the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void PublishReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Publishing the review with ID {0}.", review_id);
        client.Reviews.PublishVideoReview(TeamName, review_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>Exemple complet

Ajoutez la définition de méthode **Main** à l’espace de noms VideoTranscriptReviews de la classe Program. Pour finir, fermez la classe Program et l’espace de noms VideoTranscriptReviews.

> [!NOTE]
> Le programme utilise un exemple de transcription au format VTT. Dans une solution réelle, vous utilisez le service Azure Media Indexer pour [générer une transcription](https://docs.microsoft.com/azure/media-services/media-services-index-content) à partir d’une vidéo. 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

            02:03.000 --> 02:25.000
            This is another line in the transcript.
            ";

            AddTranscript(client, review_id, transcript);

            AddTranscriptModerationResult(client, review_id, transcript);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Exécuter le programme et réviser la sortie

Lorsque vous exécutez l’application, vous voyez un résultat sur les lignes suivantes :

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Accéder à la révision de transcription de vidéo

Accédez à la révision de transcription de vidéo dans votre outil de révision Content Moderator sur l’écran **Révision**>**Vidéo**>**Transcription**.

Vous voyez les caractéristiques suivantes :
- Les deux lignes de transcription que vous avez ajoutées
- Le terme vulgaire trouvé et mis en évidence par le service de modération de texte
- La sélection d’un texte de transcription démarre la vidéo à partir de cet horodatage

![Révision de transcriptions de vidéos pour les modérateurs humains](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment générer des [révisions de vidéos](video-reviews-quickstart-dotnet.md) dans l’outil de révision.

Consultez le didacticiel détaillé sur le développement d’une [solution de modération des vidéos complète](video-transcript-moderation-review-tutorial-dotnet.md).

[Téléchargez la solution Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pour ce démarrage rapide et d’autres démarrages rapides de Content Moderator pour .NET.
