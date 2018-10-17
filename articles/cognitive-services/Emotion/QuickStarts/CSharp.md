---
title: 'Démarrage rapide : reconnaître les émotions sur des visages dans une image - API Émotion, C#'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Émotion avec C#.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 530d05887e585884b184635e01031c1332fad3fb
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239368"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Démarrage rapide : générer une application permettant de reconnaître des émotions sur des visages dans une image.

> [!IMPORTANT]
> L'API Émotion sera déconseillée à partir du 15 février 2019. La fonction de reconnaissance des émotions est maintenant disponible de façon générale dans le cadre de l’[API Visage](https://docs.microsoft.com/azure/cognitive-services/face/).

Cet article fournit des informations et un exemple de code pour une prise en main rapide à l’aide de la [méthode Recognize de l’API Émotion](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) avec C#. Vous pouvez l’utiliser pour reconnaître les émotions exprimées par une ou plusieurs personnes sur une image.

## <a name="prerequisites"></a>Prérequis
* Obtenez le [Kit de développement logiciel (SDK) Windows de l’API Émotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/) de Cognitive Services.
* Obtenez votre [clé d’abonnement](https://azure.microsoft.com/try/cognitive-services/) gratuite.

## <a name="emotion-recognition-c-example-request"></a>Exemple de requête C# de reconnaissance des émotions

Créez une nouvelle solution Console dans Visual Studio, puis remplacez le fichier Program.cs par le code suivant. Modifiez la `string uri` pour utiliser la région où vous avez obtenu vos clés d’abonnement. Remplacez la valeur **Ocp-Apim-Subscription-Key** par votre clé d’abonnement valide. Pour trouver la clé d’abonnement, accédez au portail Azure. Dans le volet de navigation à gauche, sous la section **Clés**, accédez à votre ressource API Émotion. De même, vous pouvez obtenir la bonne connexion URI dans le volet **Vue d’ensemble** de votre ressource répertoriée sous **Point de terminaison**.

![Vos clés de ressources API](../../media/emotion-api/keys.png)

Pour traiter la réponse de votre requête, utilisez une bibliothèque telle que `Newtonsoft.Json`. De cette manière, vous pouvez gérer une chaîne JSON comme une série d’objets gérables, appelés jetons. Pour ajouter cette bibliothèque à votre package, cliquez avec le bouton droit sur votre projet dans l'Explorateur de solutions, puis sélectionnez **Gérer les packages NuGet**. Puis recherchez **Newtonsoft**. Le premier résultat devrait être **Newtonsoft.Json**. Sélectionnez **Installer**. Vous pouvez maintenant référencer cette bibliothèque dans votre application.

![Installer Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); //

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Reconnaître les exemples de réponse des émotions
Un appel réussi retourne un tableau comprenant les entrées de visages et les résultats d’émotion associés. Ils sont classés selon les dimensions des visages par ordre décroissant. Une réponse vide indique qu’aucun visage n’a été détecté. Une entrée de visage contient les champs suivants :

* faceRectangle : emplacement du rectangle du visage sur l’image
* scores : résultats d’émotion pour chaque visage sur l’image

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
