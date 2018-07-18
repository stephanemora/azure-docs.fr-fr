---
title: Démarrage rapide C# pour l’API Recherche visuelle Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Montre comment charger une image dans l’API Recherche visuelle Bing pour récupérer des informations sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: dd7531004759cdaeb59f4706dc2650d0db3c0cdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370340"
---
# <a name="your-first-bing-visual-search-query-in-c"></a>Votre première requête de recherche visuelle Bing dans C#

L’API Recherche visuelle Bing renvoie des informations sur une image que vous fournissez. Cette image est accessible via son URL, un jeton insights ou par chargement. Pour plus d’informations sur ces options, consultez la section [Qu’est-ce que l’API Recherche visuelle Bing ?](../overview.md) Cet article explique comment charger une image. Il peut se révéler utile de charger une image prise sur un appareil mobile. Par exemple, vous photographiez un monument très connu pour obtenir en retour des informations sur celui-ci. Ces informations sont parfois très variées. 

Si vous chargez une image enregistrée en local, voici les données de formulaire que vous devez inclure dans le corps du POST. Les données du formulaire doivent inclure l’en-tête Content-Disposition. Son paramètre `name` doit être défini sur « image » tandis que le paramètre `filename` peut être défini sur une chaîne quelle qu’elle soit. Le contenu du formulaire correspond au code binaire de l’image. La taille maximale de l’image que vous chargez est de 1 Mo. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Cet article inclut une application console qui envoie une requête d’API Recherche visuelle Bing et affiche les résultats de la recherche au format JSON. Alors que cette application est écrite en C#, l’API est un service web RESTful compatible avec n’importe quel langage de programmation qui peut formuler des requêtes HTTP et analyser JSON. 

L’exemple de programme utilise uniquement des classes .NET Core et s’exécute sur Windows à l’aide du CLR .NET ou sur Linux ou macOS à l’aide de [Mono](http://www.mono-project.com/).


## <a name="prerequisites"></a>Prérequis

Vous avez besoin de [Visual Studio 2017](https://www.visualstudio.com/downloads/) pour que ce code s’exécute sous Windows. (La version Community Edition gratuite fonctionne.)

Pour ce démarrage rapide, vous pouvez utiliser une clé d’abonnement en [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou en version payante.

## <a name="running-the-application"></a>Exécution de l'application

La procédure suivante montre comment envoyer le message à l’aide de HttpWebRequest. Pour obtenir un exemple qui utilise HttpClient, HttpRequestMessage et MultipartFormDataContent, consultez [Utilisation de HttpClient](#using-httpclient).

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Créez une solution de console dans Visual Studio.
1. Remplacez le contenu de `Program.cs` par le code affiché dans ce démarrage rapide.
2. Remplacez la valeur `accessKey` par votre clé d’abonnement.
2. Remplacez la valeur `imagePath` par le chemin d’accès de l’image à charger.
3. Exécutez le programme.


```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;

namespace VisualSearchUpload
{

    class Program
    {
        // **********************************************
        // *** Update and verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Set the path to the image that you want to get insights of. 
        static string imagePath = @"<pathtoimagegoeshere>";

        // Boundary strings for form data in body of POST.
        const string CRLF = "\r\n";
        static string BoundaryTemplate = "batch_{0}";
        static string StartBoundaryTemplate = "--{0}";
        static string EndBoundaryTemplate = "--{0}--";

        const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
        const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;


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

                        // Set up POST body.
                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var startFormData = BuildFormDataStart(boundary, filename);
                        var endFormData = BuildFormDataEnd(boundary);
                        var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);

                        var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);

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



        /// <summary>
        /// Verify that imagePath exists.
        /// </summary>
        static Boolean IsImagePathSet(string path)
        {
            if (string.IsNullOrEmpty(path))
                throw new ArgumentException("Image path is null or empty.");

            if (!File.Exists(path))
                throw new ArgumentException("Image path does not exist.");

            var size = new FileInfo(path).Length;

            if (size > 1000000)
                throw new ArgumentException("Image is greater than the 1 MB maximum size.");

            return true;
        }



        /// <summary>
        /// Get the binary characters of an image.
        /// </summary>
        static byte[] GetImageBinary(string path)
        {
            return File.ReadAllBytes(path);
        }


        /// <summary>
        /// Get the image's filename.
        /// </summary>
        static string GetImageFileName(string path)
        {
            return new FileInfo(path).Name;
        }


        /// <summary>
        /// Build the beginning part of the form data.
        /// </summary>
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }


        /// <summary>
        /// Build the ending part of the form data.
        /// </summary>
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }



        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
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


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
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

[Obtenir des informations sur une image à l’aide d’un jeton insights](../use-insights-token.md)  
[Didacticiel de l’application à page unique Recherche visuelle Bing](../tutorial-bing-visual-search-single-page-app.md)
[Vue d’ensemble de la Recherche visuelle Bing](../overview.md)  
[Essayer](https://aka.ms/bingvisualsearchtryforfree)  
[Obtenir une clé d’accès d’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informations de référence sur l’API Recherche visuelle Bing](https://aka.ms/bingvisualsearchreferencedoc)
