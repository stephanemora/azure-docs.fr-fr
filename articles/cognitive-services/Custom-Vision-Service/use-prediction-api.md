---
title: 'Exemple : Utiliser le point de terminaison de prédiction pour tester les images par programmation avec un classifieur - Custom Vision'
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API pour tester par programmation des images avec votre classifieur Service Vision personnalisée.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: sample
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 4b65cb6bb8a9a8b7c5546c7859a4ce1544e8feff
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218553"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Utiliser le point de terminaison de prédiction pour tester les images par programmation avec un classifieur Service Vision personnalisée

Après avoir entraîné votre modèle, vous pouvez tester par programmation les images en les soumettant à l’API Prediction. 

> [!NOTE]
> Ce document démontre l’utilisation de C# pour soumettre une image à l’API Prediction. Pour en savoir plus et obtenir des exemples d’utilisation de l’API, consultez les [Informations de référence sur l’API Prediction](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Obtenir l’URL et la clé de prédiction

À partir de la [page Web Custom Vision](https://customvision.ai), sélectionnez votre projet, puis sélectionnez l’onglet __Performance__. Pour afficher des informations sur l’utilisation de l’API Prediction, dont la valeur __Prediction-key__, sélectionnez __Prediction URL__ (URL de prédiction). Pour les projets associés à une ressource Azure, votre valeur __Prediction-key__ figure également dans la page du [portail Azure](https://portal.azure.com) de la ressource Azure associée sous __Clés__. Copiez les informations suivantes pour les utiliser dans l’application :

* __URL__ pour l’utilisation d’un __fichier image__.
* Valeur __Prediction-key__.

> [!TIP]
> S’il existe plusieurs itérations, vous pouvez savoir laquelle est utilisée en lui affectant la valeur par défaut. Sélectionnez l’itération à partir de la section __Iterations__ (Itérations), puis sélectionnez __Make default__ (Définir par défaut) en haut de la page.

![L’onglet des performances s’affiche avec un rectangle rouge entourant l’URL Prediction.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Création de l'application

1. À partir de Visual Studio, créez une application console C#.

2. Utilisez le code suivant comme corps du fichier __Program.cs__.

    > [!IMPORTANT]
    > Modifiez les informations suivantes :
    >
    > * Affectez à l’__espace de noms__ le nom de votre projet.
    > * Définissez la valeur __Prediction-Key__ précédemment reçue, dans la ligne commençant par `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Définissez la valeur __URL__ précédemment reçue, dans la ligne commençant par `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

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
        }
    }
    ```

## <a name="use-the-application"></a>Utiliser l’application

Lorsque vous exécutez l’application, vous entrez le chemin vers un fichier image. L’image est soumise à l’API et les résultats sont retournés sous la forme d’un document JSON. L’extrait JSON suivant est un exemple de la réponse :

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

[Exporter le modèle pour une utilisation mobile](export-your-model.md)
