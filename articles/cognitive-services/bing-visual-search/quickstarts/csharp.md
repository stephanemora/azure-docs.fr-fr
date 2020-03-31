---
title: 'Démarrage rapide : Obtenir des insights sur des images avec l’API REST et C# - Recherche visuelle Bing'
titleSuffix: Azure Cognitive Services
description: Découvrez comment charger une image dans l’API Recherche visuelle Bing pour récupérer des insights sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 07ecac46ab13058d308c17c5747701ee5ed577fc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446685"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Démarrage rapide : Obtenir des insights sur les images à l’aide de l’API REST Recherche visuelle Bing et de C#

Ce guide de démarrage rapide montre comment charger une image dans l’API Recherche visuelle Bing et comment visualiser les insights qu’elle retourne.

## <a name="prerequisites"></a>Prérequis

* N’importe quelle édition de [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Le [framework Json.NET](https://www.newtonsoft.com/json), disponible sous forme de package NuGet.
* Si vous utilisez Linux/MacOS, vous pouvez exécuter cette application en utilisant [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Créer et initialiser un projet

1. Dans Visual Studio, créez une solution de console nommée BingSearchApisQuickStart. Ajoutez les espaces de noms suivants dans le fichier de code principal :

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Ajoutez les variables pour votre clé d’abonnement, le point de terminaison et le chemin de l’image que vous voulez charger. `uriBase` peut être le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource :

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Créez une méthode nommée `GetImageFileName()` pour obtenir le chemin de votre image :
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Créez une méthode pour obtenir les données binaires de l’image :

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Générer les données de formulaire

Pour charger une image locale, vous commencez par créer les données de formulaire à envoyer à l’API. Les données du formulaire doivent inclure l’en-tête `Content-Disposition`, son paramètre `name` doit être défini sur « image », et le paramètre `filename` peut être défini sur n’importe quelle chaîne. Le contenu du formulaire contient les données binaires de l’image. La taille maximale de l’image que vous chargez est de 1 Mo.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Ajoutez des chaînes de limites pour mettre en forme les données du formulaire POST. Les chaînes de limites déterminent les caractères de début, de fin et de saut de ligne pour les données :

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Utilisez les variables suivantes pour ajouter des paramètres aux données du formulaire :

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Créez une fonction nommée `BuildFormDataStart()` pour créer le début des données du formulaire en utilisant les chaînes de limites et le chemin de l’image :
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Créez une fonction nommée `BuildFormDataEnd()` pour créer la fin des données du formulaire en utilisant les chaînes de limites :
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Appeler l’API Recherche visuelle Bing

1. Créez une fonction pour appeler le point de terminaison Recherche visuelle Bing et retourner la réponse JSON. La fonction prend le début et la fin des données du formulaire, un tableau d’octets contenant les données de l’image et une valeur `contentType`.

2. Utilisez un `WebRequest` pour stocker votre URI, la valeur contentType et les en-têtes.  

3. Utilisez `request.GetRequestStream()` pour écrire les données de votre formulaire et de l’image, puis pour obtenir la réponse. Votre fonction doit être similaire à celle-ci :
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Créer la méthode Main

1. Dans la méthode `Main` de votre application, obtenez le nom de fichier et les données binaires de votre image :

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Configurez le corps POST en mettant en forme ses limites. Appelez ensuite `startFormData()` et `endFormData` pour créer les données du formulaire :

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Créez la valeur `ContentType` en mettant en forme `CONTENT_TYPE_HEADER_PARAMS` et la limite des données du formulaire :

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Obtenez la réponse de l’API en appelant `BingImageSearch()` et affichez la réponse :

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Utilisation de HttpClient

Si vous utilisez `HttpClient`, vous pouvez utiliser la classe `MultipartFormDataContent` pour créer les données du formulaire. Utilisez simplement les sections de code suivantes pour remplacer les méthodes correspondantes dans l’exemple précédent.

Remplacez la méthode `Main` par ce code :

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Remplacez la méthode `BingImageSearch` par ce code :

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage de recherche visuelle](../tutorial-bing-visual-search-single-page-app.md)
