---
title: Utiliser le point de terminaison de prédiction pour tester les images par programmation avec un classifieur - Custom Vision
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API pour tester par programmation des images avec votre classifieur Service Vision personnalisée.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046068"
---
# <a name="use-your-model-with-the-prediction-api"></a>Utiliser votre modèle avec l’API de prédiction

Une fois que vous avez former votre modèle, vous pouvez tester les images par programme en leur envoi au point de terminaison d’API de prédiction.

> [!NOTE]
> Ce document démontre l’utilisation de C# pour soumettre une image à l’API Prediction. Pour plus d’informations et des exemples, consultez le [référence de l’API de prédiction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publier votre itération formée

À partir de la [page Web Custom Vision](https://customvision.ai), sélectionnez votre projet, puis sélectionnez l’onglet __Performance__.

Pour envoyer des images à l’API de prédiction, vous devez d’abord publier votre itération pour la prédiction, ce qui peut être effectuée en sélectionnant __publier__ et en spécifiant un nom pour l’itération publié. Cela rend votre modèle accessible à l’API de prédiction de votre ressource Azure de Vision personnalisée.

![L’onglet performances s’affiche avec un rectangle rouge entourant le bouton Publier.](./media/use-prediction-api/unpublished-iteration.png)

Une fois votre modèle a été publié avec succès, vous verrez une étiquette « Publié » s’affichent en regard de votre itération dans la barre latérale gauche, et son nom s’affiche dans la description de l’itération.

![L’onglet performances s’affiche avec un rectangle rouge autour de l’étiquette publié et le nom de l’itération publié.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obtenir l’URL et la clé de prédiction

Une fois que votre modèle a été publié, vous pouvez récupérer les informations requises en sélectionnant __prédiction URL__. Ceci ouvrira une boîte de dialogue avec les informations sur l’utilisation de l’API de prédiction, y compris le __prédiction URL__ et __prédiction-clé__.

![L’onglet performances s’affiche avec un rectangle rouge autour du bouton de l’URL de la prédiction.](./media/use-prediction-api/published-iteration-prediction-url.png)

![L’onglet performances s’affiche avec un rectangle rouge autour de la valeur URL de prédiction à l’aide d’un fichier image et la valeur de clé de prédiction.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Votre __clé de prédiction__ figurent également dans le [Azure portal](https://portal.azure.com) page pour la ressource Azure de Vision personnalisé associé à votre projet, sous la __clés__ panneau.

Dans ce guide, vous utilisez une image locale, donc, copiez l’URL sous **si vous avez un fichier image** dans un emplacement temporaire. Copier le correspondantes __clé de prédiction__ valeur également.

## <a name="create-the-application"></a>Création de l'application

1. Dans Visual Studio, créez un nouveau C# application console.

1. Utilisez le code suivant comme corps du fichier __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Modifiez les informations suivantes :
   * Définir le `namespace` champ au nom de votre projet.
   * Remplacez l’espace réservé `<Your prediction key>` avec la valeur de clé que vous avez récupéré précédemment.
   * Remplacez l’espace réservé `<Your prediction URL>` avec l’URL que vous avez récupéré précédemment.

## <a name="run-the-application"></a>Exécution de l'application

Lorsque vous exécutez l’application, vous êtes invité à entrer un chemin d’accès vers un fichier image dans la console. L’image est ensuite envoyée à l’API de prédiction, et les résultats de prédiction sont retournés sous forme de chaîne au format JSON. Voici un exemple de réponse.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à envoyer des images sur votre personnalisé/détecteur de classifieur d’image et recevoir une réponse par programmation avec le C# SDK. Ensuite, découvrez comment exécuter des scénarios de bout en bout avec C#, ou commencer à utiliser une autre langue SDK.

* [Démarrage rapide : .NET SDK](csharp-tutorial.md)
* [Démarrage rapide : Kit de développement logiciel (SDK) Python](python-tutorial.md)
* [Démarrage rapide : Kit de développement logiciel (SDK) Java](java-tutorial.md)
* [Démarrage rapide : SDK Node](node-tutorial.md)
* [Démarrage rapide : Kit de développement logiciel (SDK) Go](go-tutorial.md)
