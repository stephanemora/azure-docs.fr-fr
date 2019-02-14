---
title: 'Démarrage rapide : Obtenir des insights sur les images à l’aide de l’API REST Recherche visuelle Bing et de C#'
titleSuffix: Azure Cognitive Services
description: Découvrez comment charger une image dans l’API Recherche visuelle Bing pour récupérer des insights sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: d4a1ff43f6165e2620bbc3a5fbbf381ca114062e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884254"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Démarrage rapide : Obtenir des insights sur les images à l’aide de l’API REST Recherche visuelle Bing et de C#

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche visuelle Bing et voir les résultats de la recherche. Cette application C# simple charge une image dans l’API et affiche les informations retournées à son sujet.

## <a name="prerequisites"></a>Prérequis

* N’importe quelle édition de [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Le framework [Json.NET](https://www.newtonsoft.com/json), disponible sous forme de package NuGet.
* Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Créer et initialiser un projet

1. Créez une solution Console nommée `BingSearchApisQuickStart` dans Visual Studio. Ajoutez ensuite les espaces de noms suivants dans le fichier de code principal.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Ajoutez les variables pour votre clé d’abonnement, le point de terminaison et le chemin de l’image que vous souhaitez charger.

    ```csharp
        const string accessKey = "<yoursubscriptionkeygoeshere>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<pathtoimagegoeshere>";
    ```


1. Créez une méthode appelée `GetImageFileName()` pour obtenir le chemin d’accès de votre image.
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

2. Créez une méthode pour obtenir les caractères binaires de l’image.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Générer les données de formulaire

Quand vous chargez une image locale, les données de formulaire envoyées à l’API doivent être formatées correctement. Elles doivent inclure l’en-tête Content-Disposition, leur paramètre `name` doit être défini sur « image », tandis que le paramètre `filename` peut être défini sur n’importe quelle chaîne. Le contenu du formulaire contient le code binaire de l’image. La taille maximale de l’image que vous chargez est de 1 Mo.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Pour mettre en forme les données du formulaire POST correctement, ajoutez des chaînes limite, qui déterminent le début, la fin et les caractères de saut de ligne des données.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Les variables suivantes sont utilisées pour ajouter des paramètres aux données du formulaire. 

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Créez une fonction appelée `BuildFormDataStart()` pour créer la partie initiale des données de formulaire requises, en utilisant les chaînes limite et le chemin d’accès de l’image.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Créez une fonction appelée `BuildFormDataEnd()` pour créer la partie finale des données de formulaire requises, en utilisant les chaînes limite.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Appeler l’API Recherche visuelle Bing

1. Créez une fonction pour appeler le point de terminaison Recherche visuelle Bing et retourner la réponse json. La fonction doit prendre les parties initiale et finale des données du formulaire, un tableau d’octets contenant les données de l’image et une valeur contentType.

2. Utilisez un `WebRequest` pour stocker votre URI, la valeur contentType et les en-têtes.  

3. Utilisez `request.GetRequestStream()` pour écrire les données de formulaire et d’image. Ensuite, obtenez la réponse. Cette fonction doit ressembler au code ci-dessous :
        
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

## <a name="create-the-main-method"></a>Créer la méthode main

1. Dans la méthode main de votre application, obtenez le nom de fichier et le binaire de votre image. 

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Configurez le corps POST en mettant en forme ses limites. Appelez ensuite `startFormData()` et `endFormData` pour créer les données de formulaire. 

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Créez la valeur ContentType en mettant en forme `CONTENT_TYPE_HEADER_PARAMS` et la limite des données de formulaire.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Obtenez la réponse de l’API en appelant `BingImageSearch()`. Ensuite, affichez la réponse.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Utilisation de HttpClient

Si vous utilisez HttpClient, vous pouvez utiliser MultipartFormDataContent pour générer les données de formulaire. Les sections de code suivantes vous permettent de remplacer les méthodes citées dans l’exemple précédent.

Remplacez la méthode Main par ce code :

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

Remplacez la méthode BingImageSearch par ce code :

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
> [Créer une application web Recherche personnalisée](../tutorial-bing-visual-search-single-page-app.md)
