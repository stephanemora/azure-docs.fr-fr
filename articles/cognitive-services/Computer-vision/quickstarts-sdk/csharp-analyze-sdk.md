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
ms.date: 09/14/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3b6bb2ae0bcae869436eb54376b9792d8d4ccb0e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878270"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Démarrage rapide : Analyser une image à l’aide du kit de développement logiciel (SDK) Vision par ordinateur et de C#

Dans ce démarrage rapide, vous analysez une image locale ou distante pour extraire des fonctionnalités visuelles à l’aide de la bibliothèque de client Windows de l’API Vision par ordinateur.

## <a name="prerequisites"></a>Prérequis

* Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).
* N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* Le package NuGet de la bibliothèque de client [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Il n’est pas nécessaire de télécharger le package. Vous trouverez ci-dessous des instructions d’installation.

## <a name="analyzeimageasync-method"></a>Méthode AnalyzeImageAsync

> [!TIP]
> Récupérer le dernier code en tant que solution Visual Studio à partir de [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

Les méthodes `AnalyzeImageAsync` et `AnalyzeImageInStreamAsync` encapsulent [l’API Analyser une image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) pour les images distantes et locales, respectivement. Vous pouvez utiliser ces méthodes pour extraire des fonctionnalités visuelles en fonction du contenu de l’image et choisir les fonctionnalités à retourner, notamment :

* Une liste détaillée des balises liées au contenu de l’image.
* Une description du contenu de l’image dans une phrase complète.
* Les coordonnées, le sexe et l’âge de n’importe quel visage contenu dans l’image.
* Le type d’image (images clipart ou dessin au trait).
* La couleur dominante, la couleur d’accentuation, et si une image est en noir et blanc.
* La catégorie définie dans cette [taxonomie](../Category-Taxonomy.md).
* L’image contient-elle un contenu pour adultes ou sexuellement suggestif ?

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Dans Visual Studio, créez une application de console Visual C#.
1. Installez le package NuGet de la bibliothèque de client de l’API Vision par ordinateur.
    1. Dans le menu, cliquez sur **Outils**, sélectionnez **Gestionnaire de packages NuGet**, puis **Gérer les packages NuGet de la solution**.
    1. Cliquez sur l’onglet **Parcourir**, puis dans la zone de **recherche**, tapez « Microsoft.Azure.CognitiveServices.Vision.ComputerVision ».
    1. Sélectionnez **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** lorsqu’il s’affiche, puis cliquez sur la case à cocher en regard du nom de votre projet, et ensuite sur **Installer**.
1. Remplacez `Program.cs` par le code suivant.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Changez `computerVision.Endpoint` pour la région Azure associée à vos clés d’abonnement, si nécessaire.
1. Remplacez `<LocalImage>` par le chemin d’accès et le nom d’une image locale.
1. Si vous le souhaitez, définissez `remoteImageUrl` sur une autre image.
1. Exécutez le programme.

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
            "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

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
            Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
        }
    }
}
```

## <a name="analyzeimageasync-response"></a>Réponse AnalyzeImageAsync

Une réponse correcte affiche la légende la plus pertinente pour chaque image.

Consultez [Démarrages rapides de l’API : Analyser une image locale avec C#](../QuickStarts/CSharp-analyze.md#examine-the-response) pour obtenir un exemple de sortie JSON brute.

```
http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit.

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
