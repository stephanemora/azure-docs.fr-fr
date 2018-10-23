---
title: 'Démarrage rapide : Vérifier le contenu d’une image en C# - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Comment vérifier le contenu d’une image à l’aide du Kit de développement logiciel (SDK) Content Moderator pour C#
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: 4973d78eac02aed42689bf5742155c375d5f78ae
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309295"
---
# <a name="quickstart-check-image-content-in-c"></a>Démarrage rapide : Vérifier le contenu d’une image en C# 

Cet article fournit des informations et des exemples de code qui vont vous aider à prendre en main le [Kit de développement logiciel (SDK) Content Moderator pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) afin de vérifier une image et détecter : 

- le contenu osé ou réservé aux adultes,
- le texte pouvant être extrait,
- les visages humains.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="sign-up-for-content-moderator-services"></a>S’inscrire aux services Content Moderator

Avant de pouvoir utiliser les services Content Moderator par le biais de l’API REST ou du Kit de développement logiciel (SDK), vous avez besoin d’une clé API et de la région de votre compte d’API. Dans le [Portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator), abonnez-vous à Content Moderator pour obtenir ces informations.

## <a name="create-your-visual-studio-project"></a>Créer votre projet Visual Studio

1. Ajoutez un nouveau projet **Application console (.NET Framework)** à votre solution.

   Dans l’exemple de code, nommez le projet **ImageModeration**.

1. Sélectionnez ce projet comme unique projet de démarrage de la solution.


### <a name="install-required-packages"></a>Installer les packages nécessaires

Installez les packages NuGet suivants :

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Mettre à jour les instructions using du programme

Ajoutez les instructions `using` suivantes.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Créer le client Content Moderator

Ajoutez le code suivant afin de créer un client Content Moderator pour votre abonnement.

> [!IMPORTANT]
> Mettez à jour les champs **AzureRegion** et **CMSubscriptionKey** avec les valeurs de votre identificateur de région et de votre clé d’abonnement.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account, 
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>Initialiser les paramètres spécifiques de l’application

Ajoutez les champs statiques suivants à la classe **Program** dans Program.cs.

```csharp
///<summary>
///The name of the file that contains the image URLs to evaluate.
///</summary>
///<remarks>You will need to create an input file and update 
///this path accordingly. Paths are relative to the execution directory.
///</remarks>
private static string ImageUrlFile = "ImageFiles.txt";

///<summary>
///The name of the file to contain the output from the evaluation.
///</summary>
///<remarks>Paths are relative to the execution directory.
///</remarks>
private static string OutputFile = "ModerationOutput.json";
```

Créez un fichier d’entrée _ImageFiles.txt_ et ajoutez les URL des images à analyser. Ce démarrage rapide utilise les deux URL suivantes pour générer son exemple de sortie.
- https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
- https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png

## <a name="create-a-class-to-handle-results"></a>Créer une classe pour traiter les résultats

Ajoutez la classe suivante à la classe **Program**. Vous utilisez une instance de cette classe pour enregistrer les résultats de modération de chacune des images vérifiées.

```csharp
/// <summary>
/// Contains the image moderation results for an image, 
/// including text and face detection results.
/// </summary>
public class EvaluationData
{
    /// <summary>
    /// The URL of the evaluated image.
    /// </summary>
    public string ImageUrl;

    /// <summary>
    /// The image moderation results.
    /// </summary>
    public Evaluate ImageModeration;

    /// <summary>
    /// The text detection results.
    /// </summary>
    public OCR TextDetection;

    /// <summary>
    /// The face detection results;
    /// </summary>
    public FoundFaces FaceDetection;
}
```

## <a name="create-the-image-evaluation-method"></a>Créer la méthode d’évaluation d’image

Ajoutez la méthode suivante à la classe **Program**. Cette méthode évalue une seule image et renvoie les résultats d’évaluation.

> [!NOTE]
> Votre clé de service Content Moderator a une limite de fréquence des requêtes par seconde. Si vous dépassez cette limite, le Kit de développement logiciel (SDK) lève une exception avec le code d’erreur 429. Une clé de niveau gratuit a une limite de fréquence d’une requête par seconde.

```csharp
/// <summary>
/// Evaluates an image using the Image Moderation APIs.
/// </summary>
/// <param name="client">The Content Moderator API wrapper to use.</param>
/// <param name="imageUrl">The URL of the image to evaluate.</param>
/// <returns>Aggregated image moderation results for the image.</returns>
/// <remarks>This method throttles calls to the API.
/// Your Content Moderator service key will have a requests per second (RPS)
/// rate limit, and the SDK will throw an exception with a 429 error code 
/// if you exceed that limit. A free tier key has a 1 RPS rate limit.
/// </remarks>
private static EvaluationData EvaluateImage(
  ContentModeratorClient client, string imageUrl)
{
    var url = new ImageUrl("URL", imageUrl.Trim());

    var imageData = new EvaluationData();

    imageData.ImageUrl = url.Value;

  // Evaluate for adult and racy content.
    imageData.ImageModeration =
        client.ImageModeration.EvaluateUrlInput("application/json", url, true);
    Thread.Sleep(1000);

    // Detect and extract text.
    imageData.TextDetection =
        client.ImageModeration.OCRUrlInput("eng", "application/json", url, true);
    Thread.Sleep(1000);

    // Detect faces.
    imageData.FaceDetection =
        client.ImageModeration.FindFacesUrlInput("application/json", url, true);
    Thread.Sleep(1000);

    return imageData;
}
```

La méthode **EvaluateUrlInput** est un wrapper pour l’API REST de modération d’images.
La valeur renvoyée contient l’objet renvoyé suite à l’appel d’API.

La méthode **OCRUrlInput** est un wrapper pour l’API REST d’OCR d’image.
La valeur renvoyée contient l’objet renvoyé suite à l’appel d’API.

La méthode **FindFacesUrlInput** est un wrapper pour l’API REST de recherche de visages dans une image.
La valeur renvoyée contient l’objet renvoyé suite à l’appel d’API.

## <a name="evaluate-the-images-in-your-code"></a>Évaluer les images dans votre code

Ajoutez le code suivant à la méthode **Main**.

```csharp
// Create an object to store the image moderation results.
List<EvaluationData> evaluationData = new List<EvaluationData>();

// Create an instance of the Content Moderator API wrapper.
using (var client = Clients.NewClient())
{
    // Read image URLs from the input file and evaluate each one.
    using (StreamReader inputReader = new StreamReader(ImageUrlFile))
    {
        while (!inputReader.EndOfStream)
        {
            string line = inputReader.ReadLine().Trim();
            if (line != String.Empty)
            {
                EvaluationData imageData = EvaluateImage(client, line);
                evaluationData.Add(imageData);
            }
        }
    }
}

// Save the moderation results to a file.
using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
{
    outputWriter.WriteLine(JsonConvert.SerializeObject(
        evaluationData, Formatting.Indented));

    outputWriter.Flush();
    outputWriter.Close();
}
```

## <a name="run-the-program-and-review-the-output"></a>Exécuter le programme et examiner la sortie

Ouvrez votre fichier _ModerationOutput.json_ pour afficher le contenu de sortie. Cela doit ressembler au contenu ci-dessous. Notez que chaque image contient différentes sections pour `ImageModeration`, `FaceDetection` et `TextDetection`, qui correspondent aux trois appels d’API dans votre méthode **EvaluateImage**.

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps---get-the-source-code"></a>Étapes suivantes - Obtenir le code source

Obtenez le [SDK Content Moderator pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) et la [solution Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pour ce guide de démarrage rapide et d’autres guides de démarrage rapide Content Moderator pour .NET, puis commencez votre intégration.
