---
title: 'Démarrage rapide : Analyser une image - SDK, C#'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous analysez une image à l’aide de la bibliothèque de client Windows en C# de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 636072b011c258e8e5ecb05b761bfab8d67e439a
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609385"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Démarrage rapide : Analyser une image à l’aide du kit de développement logiciel (SDK) Vision par ordinateur et de C#

Dans ce guide de démarrage rapide, vous allez analyser une image locale et une image distante pour extraire des caractéristiques visuelles à l’aide de la bibliothèque de client Vision par ordinateur pour C#. Si vous le souhaitez, vous pouvez télécharger le code présenté dans ce guide sous la forme d’un exemple d’application complet à partir du dépôt [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) sur GitHub.

## <a name="prerequisites"></a>Prérequis

* Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).
* N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* Le package NuGet de la bibliothèque de client [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Il n’est pas nécessaire de télécharger le package. Vous trouverez ci-dessous des instructions d’installation.

## <a name="create-and-run-the-sample-application"></a>Créer et exécuter l’exemple d’application

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Dans Visual Studio, créez une application de console Visual C#.
1. Installez le package NuGet de la bibliothèque de client de l’API Vision par ordinateur.
    1. Dans le menu, cliquez sur **Outils**, sélectionnez **Gestionnaire de packages NuGet**, puis **Gérer les packages NuGet de la solution**.
    1. Cliquez sur l’onglet **Parcourir**, puis dans la zone de **recherche**, tapez « Microsoft.Azure.CognitiveServices.Vision.ComputerVision ».
    1. Sélectionnez **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** lorsqu’il s’affiche, puis cliquez sur la case à cocher en regard du nom de votre projet, et ensuite sur **Installer**.
1. Remplacez le contenu de *Program.cs* par le code suivant. Les méthodes `AnalyzeImageAsync` et `AnalyzeImageInStreamAsync` incluent dans un wrapper [l’API REST Analyser une image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) pour les images distantes et locales, respectivement.

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageAnalyze
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

            // Specify the features to return
            private static readonly List<VisualFeatureTypes> features =
                new List<VisualFeatureTypes>()
            {
                VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
                VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
                VisualFeatureTypes.Tags
            };

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
                var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
                var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Analyze a remote image
            private static async Task AnalyzeRemoteAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                ImageAnalysis analysis =
                    await computerVision.AnalyzeImageAsync(imageUrl, features);
                DisplayResults(analysis, imageUrl);
            }

            // Analyze a local image
            private static async Task AnalyzeLocalAsync(
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
                    ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                        imageStream, features);
                    DisplayResults(analysis, imagePath);
                }
            }

            // Display the most relevant caption for the image
            private static void DisplayResults(ImageAnalysis analysis, string imageUri)
            {
                Console.WriteLine(imageUri);
                if (analysis.Description.Captions.Count != 0)
                {
                    Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
                }
                else
                {
                    Console.WriteLine("No description generated.");
                }
            }
        }
    }
    ```

1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Changez `computerVision.Endpoint` pour la région Azure associée à vos clés d’abonnement, si nécessaire.
1. Remplacez `<LocalImage>` par le chemin d’accès et le nom d’une image locale.
1. Si vous le souhaitez, définissez `remoteImageUrl` avec l’URL d’une autre image.
1. Exécutez le programme.

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte affiche la légende la plus pertinente pour chaque image. Vous pouvez changer la méthode `DisplayResults` pour générer différentes données d’image. Pour en savoir plus, consultez la méthode [AnalyzeLocalAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions.analyzeimageinstreamasync?view=azure-dotnet).

Consultez [Démarrages rapides de l’API : Analyser une image locale avec C#](../QuickStarts/CSharp-analyze.md#examine-the-response) pour obtenir un exemple de sortie JSON brute.

```console
https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit.

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
