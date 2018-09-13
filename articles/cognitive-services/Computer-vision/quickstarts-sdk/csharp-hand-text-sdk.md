---
title: 'API Vision par ordinateur avec C# - Démarrage rapide : SDK, texte manuscrit | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous extrayez du texte manuscrit à partir d’une image à l’aide de la bibliothèque de client C# Windows de l’API Vision par ordinateur dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 7eb87e3d4b1703bf1ee0e30c930b0bc724b7f22f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770101"
---
# <a name="quickstart-extract-handwritten-text---sdk-c35"></a>Démarrage rapide : Extraire du texte manuscrit - SDK, C&#35;

Dans ce démarrage rapide, vous extrayez du texte manuscrit à partir d’une image à l’aide de la bibliothèque de client Windows de l’API Vision par ordinateur.

## <a name="prerequisites"></a>Prérequis

* Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).
* N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* Le package NuGet de la bibliothèque de client [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Il n’est pas nécessaire de télécharger le package. Vous trouverez ci-dessous des instructions d’installation.

## <a name="recognizetextasync-method"></a>Méthode RecognizeTextAsync

Les méthodes `RecognizeTextAsync` et `RecognizeTextInStreamAsync` encapsulent [l’API Reconnaître le texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) pour les images locales et distantes, respectivement. La méthode `GetTextOperationResultAsync` encapsule [l’API Get Reconnaître les résultats des opérations sur le texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201).  Vous pouvez utiliser ces méthodes pour détecter le texte manuscrit dans une image et extraire les caractères reconnus dans un flux de caractères exploitable automatiquement.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Dans Visual Studio, créez une application console Visual C#.
1. Installez le package NuGet de la bibliothèque de client de l’API Vision par ordinateur.
    1. Dans le menu, cliquez sur **Outils**, sélectionnez **Gestionnaire de packages NuGet**, puis **Gérer les packages NuGet de la solution**.
    1. Cliquez sur l’onglet **Parcourir**, puis sur le type de zone de **recherche** « Microsoft.Azure.CognitiveServices.Vision.ComputerVision ».
    1. Sélectionnez le type **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** lorsqu’il s’affiche, puis cliquez sur la case à cocher en regard du nom de votre projet, et ensuite sur **Installer**.
1. Remplacez `Program.cs` par le code suivant.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez `computerVision.AzureRegion = AzureRegions.Westcentralus` par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
1. Remplacez `<LocalImage>` par le chemin d’accès et le nom d’une image locale.
1. Si vous le souhaitez, définissez `remoteImageUrl` sur une autre image.
1. Exécutez le programme.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ImageHandText
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
            "Cursive_Writing_on_Notebook_paper.jpg/" +
            "800px-Cursive_Writing_on_Notebook_paper.jpg";

        private const int numberOfCharsInOperationId = 36;

        static void Main(string[] args)
        {
            ComputerVisionAPI computerVision = new ComputerVisionAPI(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "Westcentralus" with "Westus".
            //
            // Free trial subscription keys are generated in the westcentralus
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.AzureRegion = AzureRegions.Westcentralus;

            Console.WriteLine("Images being analyzed ...");
            var t1 = ExtractRemoteHandTextAsync(computerVision, remoteImageUrl);
            var t2 = ExtractLocalHandTextAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Recognize text from a remote image
        private static async Task ExtractRemoteHandTextAsync(
            ComputerVisionAPI computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            // Start the async process to recognize the text
            RecognizeTextHeaders textHeaders = await computerVision.RecognizeTextAsync(
                    imageUrl, TextRecognitionMode.Handwritten);

            await GetTextAsync(computerVision, textHeaders.OperationLocation);
        }

        // Recognize text from a local image
        private static async Task ExtractLocalHandTextAsync(
            ComputerVisionAPI computerVision, string imagePath)
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
                        imageStream, TextRecognitionMode.Handwritten);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }
        }

        // Retrieve the recognized text
        private static async Task GetTextAsync(
            ComputerVisionAPI computerVision, string operationLocation)
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
            foreach(Line line in lines)
            {
                Console.WriteLine(line.Text);
            }
            Console.WriteLine();
        }
    }
}
```

## <a name="recognizetextasync-response"></a>Réponse RecognizeTextAsync

Une réponse réussie affiche les lignes du texte reconnu pour chaque image.

Consultez [Démarrages rapides de l’API : extraire le texte manuscrit avec C#](../QuickStarts/CSharp-hand-text.md#recognize-text-response) pour obtenir un exemple de sortie JSON brute.

```cmd
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

## <a name="next-steps"></a>Étapes suivantes

Explorer les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des monuments, créer une miniature et extraire le texte imprimé et manuscrit.

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
