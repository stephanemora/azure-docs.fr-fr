---
title: 'API Vision par ordinateur avec C# - Démarrage rapide : OCR | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous extrayez le texte imprimé d’une image à l’aide de l’API Vision par ordinateur avec C# dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 46193e7aa27285a887005a67bb662449e18d7b36
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770334"
---
# <a name="quickstart-extract-printed-text-ocr---rest-c35"></a>Démarrage rapide : Extraire le texte imprimé (OCR) - REST, C&#35;

Dans ce démarrage rapide, vous extrayez le texte imprimé selon une technique également appelée reconnaissance optique de caractères (OCR) à partir d’une image, à l’aide de l’API Vision par ordinateur.

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="ocr-request"></a>Requête OCR

Avec la [méthode OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), vous pouvez détecter le texte imprimé dans une image et extraire les caractères reconnus dans un flux de caractères exploitable automatiquement.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Dans Visual Studio, créez une application de console Visual C#.
1. Installez le package NuGet Newtonsoft.Json.
    1. Dans le menu, cliquez sur **Outils**, sélectionnez **Gestionnaire de packages NuGet**, puis **Gérer les packages NuGet de la solution**.
    1. Cliquez sur l’onglet **Parcourir**, puis, dans la zone de **recherche**, tapez « Newtonsoft.Json ».
    1. Lorsqu’elle apparaît, sélectionnez l’option **Newtonsoft.Json**, puis cliquez sur la case à cocher en regard du nom de votre projet, puis sur **Installer**.
1. Remplacez `Program.cs` par le code suivant.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez la valeur `uriBase` par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
1. Exécutez le programme.
1. À l’invite, entrez le chemin d’accès à une image locale.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to
        // get your subscription keys. For example, if you got your
        // subscription keys from westus, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Optical Character Recognition:");
            Console.Write("Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeOCRRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text visible in the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with printed text.</param>
        static async Task MakeOCRRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                string requestParameters = "language=unk&detectOrientation=true";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body. Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Make the REST API call.
                    response = await client.PostAsync(uri, content);
                }

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="ocr-response"></a>Réponse OCR

En cas de réussite, les résultats de la reconnaissance optique de caractères renvoyés incluent du texte, ainsi qu’un rectangle englobant pour les régions, les lignes et les mots, par exemple :

```json
{
   "language": "en",
   "textAngle": -1.5000000000000335,
   "orientation": "Up",
   "regions": [
      {
         "boundingBox": "154,49,351,575",
         "lines": [
            {
               "boundingBox": "165,49,340,117",
               "words": [
                  {
                     "boundingBox": "165,49,63,109",
                     "text": "A"
                  },
                  {
                     "boundingBox": "261,50,244,116",
                     "text": "GOAL"
                  }
               ]
            },
            {
               "boundingBox": "165,169,339,93",
               "words": [
                  {
                     "boundingBox": "165,169,339,93",
                     "text": "WITHOUT"
                  }
               ]
            },
            {
               "boundingBox": "159,264,342,117",
               "words": [
                  {
                     "boundingBox": "159,264,64,110",
                     "text": "A"
                  },
                  {
                     "boundingBox": "255,266,246,115",
                     "text": "PLAN"
                  }
               ]
            },
            {
               "boundingBox": "161,384,338,119",
               "words": [
                  {
                     "boundingBox": "161,384,86,113",
                     "text": "IS"
                  },
                  {
                     "boundingBox": "274,387,225,116",
                     "text": "JUST"
                  }
               ]
            },
            {
               "boundingBox": "154,506,341,118",
               "words": [
                  {
                     "boundingBox": "154,506,62,111",
                     "text": "A"
                  },
                  {
                     "boundingBox": "248,508,247,116",
                     "text": "WISH"
                  }
               ]
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Windows de base qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement les API Vision par ordinateur, essayez la [console de test d’API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutoriel sur l’API Vision par ordinateur C&#35;](../Tutorials/CSharpTutorial.md)
