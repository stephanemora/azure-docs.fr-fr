---
title: 'Exemple : Utiliser le point de terminaison de prédiction pour tester les images par programmation avec un classifieur - Custom Vision'
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API pour tester par programmation des images avec votre classifieur Service Vision personnalisée.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472721"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Utiliser votre modèle avec l’API de prédiction

Après avoir entraîné votre modèle, vous pouvez tester par programmation les images en les soumettant à l’API Prediction.

> [!NOTE]
> Ce document démontre l’utilisation de C# pour soumettre une image à l’API Prediction. Pour en savoir plus et obtenir des exemples d’utilisation de l’API, consultez les [Informations de référence sur l’API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publier votre itération formée

À partir de la [page Web Custom Vision](https://customvision.ai), sélectionnez votre projet, puis sélectionnez l’onglet __Performance__.

Pour envoyer des images à l’API de prédiction, vous devez d’abord publier votre itération pour la prédiction, ce qui peut être effectuée en sélectionnant __publier__ et en spécifiant un nom pour l’itération publié. Cela permettra à votre modèle soit accessible à l’API de prédiction de votre ressource Azure de Vision personnalisée. 

![L’onglet performances s’affiche avec un rectangle rouge entourant le bouton Publier.](./media/use-prediction-api/unpublished-iteration.png)

Une fois que votre modèle a été publié avec succès, vous verrez une étiquette « Publié » s’affichent en regard de votre itération dans la barre latérale gauche, ainsi que le nom de l’itération publiée dans la description de l’itération.

![L’onglet performances s’affiche avec un rectangle rouge autour de l’étiquette publié et le nom de l’itération publié.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obtenir l’URL et la clé de prédiction

Une fois que votre modèle a été publié, vous pouvez récupérer des informations sur l’utilisation de l’API de prédiction en sélectionnant __prédiction URL__. Ceci ouvrira une boîte de dialogue comme celui illustré ci-dessous, avec des informations sur l’utilisation de l’API de prédiction, y compris le __prédiction URL__ et __prédiction-clé__.

![L’onglet performances s’affiche avec un rectangle rouge autour du bouton de l’URL de la prédiction.](./media/use-prediction-api/published-iteration-prediction-url.png)

![L’onglet performances s’affiche avec un rectangle rouge autour de la valeur URL de prédiction à l’aide d’un fichier image et la valeur de clé de prédiction.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Votre __clé de prédiction__ figurent également dans le [Azure Portal](https://portal.azure.com) page pour la ressource Azure de Vision personnalisé associé à votre projet, sous __clés__. 

À partir de la boîte de dialogue, copiez les informations suivantes pour une utilisation dans l’application :

* __URL de la prédiction__ pour utiliser un __fichier image__.
* __Clé de prédiction__ valeur.

## <a name="create-the-application"></a>Création de l'application

1. À partir de Visual Studio, créez une application console C#.

1. Utilisez le code suivant comme corps du fichier __Program.cs__.

    > [!IMPORTANT]
    > Modifiez les informations suivantes :
    >
    > * Affectez à l’__espace de noms__ le nom de votre projet.
    > * Définir le __clé de prédiction__ valeur que vous avez récupéré précédemment dans la ligne qui commence par `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Définir le __prédiction URL__ valeur que vous avez récupéré précédemment dans la ligne qui commence par `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Utiliser l’application

Lorsque vous exécutez l’application, vous entrerez le chemin d’accès vers un fichier image dans la console. L’image est soumise à l’API de prédiction et les résultats de prédiction sont retournées comme un document JSON. Le code JSON suivant est un exemple de la réponse.

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

[Exporter le modèle pour une utilisation mobile](export-your-model.md)

[Bien démarrer avec les kits de développement logiciel .NET](csharp-tutorial.md)

[Bien démarrer avec les kits de développement logiciel Python](python-tutorial.md)

[Bien démarrer avec les kits de développement Java](java-tutorial.md)

[Bien démarrer avec les kits de développement de nœud](node-tutorial.md)

[Prise en main passez kits de développement logiciel](go-tutorial.md)
