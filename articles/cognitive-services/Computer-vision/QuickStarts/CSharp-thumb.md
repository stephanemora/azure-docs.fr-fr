---
title: 'Démarrage rapide : Générer une miniature - REST, C#'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une miniature d’une image en utilisant l’API Vision par ordinateur avec C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 195f085a1e1b7e991094db666aa9787b09d5a33c
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95746572"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-c"></a>Démarrage rapide : Générer une miniature à l’aide de l’API REST Vision par ordinateur et de C#

Dans ce guide de démarrage rapide, vous générez une miniature à partir d’une image en utilisant l’API REST de Vision par ordinateur. Avec la méthode [Obtenir une miniature](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c), vous pouvez générer une miniature d’une image. Vous spécifiez la hauteur et la largeur, qui peuvent différer des proportions de l’image d’entrée. L’API Vision par ordinateur utilise le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage en fonction de cette région.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Vous devez disposer de [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou d’une version ultérieure
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* [Créez des variables d’environnement](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour la clé et l’URL du point de terminaison, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>Créer et exécuter l’exemple d’application

Pour créer l’exemple dans Visual Studio, effectuez les étapes suivantes :

1. Créez une solution Visual Studio dans Visual Studio en utilisant le modèle d’application console Visual C#.
1. Installez le package NuGet Newtonsoft.Json.
    1. Dans le menu, cliquez sur **Outils**, sélectionnez **Gestionnaire de packages NuGet**, puis **Gérer les packages NuGet de la solution**.
    1. Cliquez sur l’onglet **Parcourir**, puis, dans la zone de **recherche**, tapez « Newtonsoft.Json ».
    1. Lorsqu’elle apparaît, sélectionnez l’option **Newtonsoft.Json**, puis cliquez sur la case à cocher en regard du nom de votre projet, puis sur **Installer**.
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
        // Add your Computer Vision subscription key and base endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");
        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // The GenerateThumbnail method endpoint
        static string uriBase = endpoint + "vision/v3.1/generateThumbnail";
        // Add an image to your bin/debug/netcoreappX.X folder, then add the image name (with extension), here
        static string imageFilePath = @"my-image-name";

        public static void Main()
        {

            MakeThumbNailRequest(imageFilePath).Wait();

            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets a thumbnail image from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to use to create the thumbnail image.</param>
        static async Task MakeThumbNailRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                // The width and height parameters specify a thumbnail that's 
                // 200 pixels wide and 150 pixels high.
                // The smartCropping parameter is set to true, to enable smart cropping.
                string requestParameters = "width=200&height=150&smartCropping=true";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Check the HTTP status code of the response. If successful, display
                // display the response and save the thumbnail.
                if (response.IsSuccessStatusCode)
                {
                    // Display the response data.
                    Console.WriteLine("\nResponse:\n{0}", response);

                    // Get the image data for the thumbnail from the response.
                    byte[] thumbnailImageData =
                        await response.Content.ReadAsByteArrayAsync();

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    // Note: This will overwrite an existing file of the same name.
                    string thumbnailFilePath =
                        imageFilePath.Insert(imageFilePath.Length - 4, "_thumb");
                    File.WriteAllBytes(thumbnailFilePath, thumbnailImageData);
                    Console.WriteLine("\nThumbnail written to: {0}", thumbnailFilePath);
                }
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                }
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
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse réussie est retournée sous forme de données binaires qui représentent les données image de la miniature. Si la requête réussit, la miniature est enregistrée dans le même dossier que l’image locale, avec le nom d’origine contenant le suffixe « _thumb ». Si la requête échoue, la réponse contient un code d’erreur et un message pour vous aider à déterminer la cause du problème.

L’exemple d’application affiche une réponse réussie dans la fenêtre de console, comme dans l’exemple suivant :

```console
Response:

StatusCode: 200, ReasonPhrase: 'OK', Version: 1.1, Content: System.Net.Http.StreamContent, Headers:
{
  Pragma: no-cache
  apim-request-id: 131eb5b4-5807-466d-9656-4c1ef0a64c9b
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  x-content-type-options: nosniff
  Cache-Control: no-cache
  Date: Tue, 06 Jun 2017 20:54:07 GMT
  X-AspNet-Version: 4.0.30319
  X-Powered-By: ASP.NET
  Content-Length: 5800
  Content-Type: image/jpeg
  Expires: -1
}
```

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Windows de base qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement les API Vision par ordinateur, essayez la [console de test d’API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).

> [!div class="nextstepaction"]
> [Tutoriel sur l’API Vision par ordinateur C&#35;](../Tutorials/CSharpTutorial.md)