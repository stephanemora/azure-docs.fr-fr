---
title: 'Démarrage rapide : Extraire du texte manuscrit - SDK, C#'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous extrayez du texte à partir d’une image, à l’aide de la bibliothèque de client Windows en C# de la Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d71a566d5c6dc5505b4bd939e294f8428e9a5b93
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312905"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>Démarrage rapide : Extraire du texte à l’aide du kit de développement logiciel (SDK) Vision par ordinateur et de C#

Dans ce guide de démarrage rapide, vous allez extraire du texte manuscrit ou imprimé à partir d’une image à l’aide du SDK Vision par ordinateur pour C#. Si vous le souhaitez, vous pouvez télécharger le code présenté dans ce guide sous la forme d’un exemple d’application complet à partir du dépôt [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) sur GitHub.

## <a name="prerequisites"></a>Prérequis

* Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).
* N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* Le package NuGet de la bibliothèque de client [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Il n’est pas nécessaire de télécharger le package. Vous trouverez ci-dessous des instructions d’installation.

## <a name="create-and-run-the-sample-app"></a>Création et exécution de l’application exemple

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Dans Visual Studio, créez une application de console Visual C#.
1. Installez le package NuGet de la bibliothèque de client de l’API Vision par ordinateur.
    1. Dans le menu, cliquez sur **Outils**, sélectionnez **Gestionnaire de packages NuGet**, puis **Gérer les packages NuGet de la solution**.
    1. Cliquez sur l’onglet **Parcourir**, puis dans la zone de **recherche**, tapez « Microsoft.Azure.CognitiveServices.Vision.ComputerVision ».
    1. Sélectionnez **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** lorsqu’il s’affiche, puis cliquez sur la case à cocher en regard du nom de votre projet, et ensuite sur **Installer**.
1. Remplacez `Program.cs` par le code suivant. Les méthodes `RecognizeTextAsync` et `RecognizeTextInStreamAsync` encapsulent [l’API Reconnaître le texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) pour les images locales et distantes, respectivement. La méthode `GetTextOperationResultAsync` encapsule [l’API Get Reconnaître les résultats des opérations sur le texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201).

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ExtractText
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // For printed text, change to TextRecognitionMode.Printed
            private const TextRecognitionMode textRecognitionMode =
                TextRecognitionMode.Handwritten;

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
                "Cursive_Writing_on_Notebook_paper.jpg/" +
                "800px-Cursive_Writing_on_Notebook_paper.jpg";

            private const int numberOfCharsInOperationId = 36;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the "westus"
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
                var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Recognize text from a remote image
            private static async Task ExtractRemoteTextAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                // Start the async process to recognize the text
                RecognizeTextHeaders textHeaders =
                    await computerVision.RecognizeTextAsync(
                        imageUrl, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }

            // Recognize text from a local image
            private static async Task ExtractLocalTextAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    // Start the async process to recognize the text
                    RecognizeTextInStreamHeaders textHeaders =
                        await computerVision.RecognizeTextInStreamAsync(
                            imageStream, textRecognitionMode);

                    await GetTextAsync(computerVision, textHeaders.OperationLocation);
                }
            }

            // Retrieve the recognized text
            private static async Task GetTextAsync(
                ComputerVisionClient computerVision, string operationLocation)
            {
                // Retrieve the URI where the recognized text will be
                // stored from the Operation-Location header
                string operationId = operationLocation.Substring(
                    operationLocation.Length - numberOfCharsInOperationId);

                Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
                TextOperationResult result =
                    await computerVision.GetTextOperationResultAsync(operationId);

                // Wait for the operation to complete
                int i = 0;
                int maxRetries = 10;
                while ((result.Status == TextOperationStatusCodes.Running ||
                        result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
                {
                    Console.WriteLine(
                        "Server status: {0}, waiting {1} seconds...", result.Status, i);
                    await Task.Delay(1000);

                    result = await computerVision.GetTextOperationResultAsync(operationId);
                }

                // Display the results
                Console.WriteLine();
                var lines = result.RecognitionResult.Lines;
                foreach (Line line in lines)
                {
                    Console.WriteLine(line.Text);
                }
                Console.WriteLine();
            }
        }
    }
    ```

1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Changez `computerVision.Endpoint` pour la région Azure associée à vos clés d’abonnement, si nécessaire.
1. Le cas échéant, définissez `textRecognitionMode` sur `TextRecognitionMode.Printed`.
1. Remplacez `<LocalImage>` par le chemin d’accès et le nom d’une image locale.
1. Si vous le souhaitez, définissez `remoteImageUrl` sur une autre image.
1. Exécutez le programme.


## <a name="examine-the-response"></a>Examiner la réponse

Une réponse réussie imprime les lignes du texte reconnu pour chaque image.

```console
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

Consultez [Démarrage rapide : Extraire le texte manuscrit - REST, C#](../QuickStarts/CSharp-hand-text.md#examine-the-response) pour obtenir un exemple de la sortie JSON brute à partir de l’appel API.

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit.

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
