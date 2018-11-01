---
title: Utilisation d’un jeton insights - Recherche visuelle Bing
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser le jeton insights d’une image avec l’API Recherche visuelle Bing pour obtenir des insights sur une image.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: conceptual
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: c7464558dd7227303fd7a3cc8be71ca5e2d2cfca
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414962"
---
# <a name="using-an-insights-token-to-get-insights-about-an-image"></a>Utilisation d’un jeton insights pour obtenir des informations sur une image

L’API Recherche visuelle Bing renvoie des informations sur une image que vous fournissez. Cette image est accessible via son URL, un jeton insights ou par chargement. Pour plus d’informations sur ces options, consultez la section [Qu’est-ce que l’API Recherche visuelle Bing ?](overview.md). Cet article utilise un jeton insights pour la démonstration. Pour obtenir des exemples qui illustrent le téléchargement d’une image pour obtenir des informations, consultez les démarrages rapides ([C#](quickstarts\csharp.md) | [Java](quickstarts\java.md) | [Node.js](quickstarts\nodejs.md) | [Python](quickstarts\python.md)).


Si vous envoyez à la recherche visuelle un jeton d’image ou une URL, voici les données de formulaire que vous devez inclure dans le corps de la requête POST. Les données de formulaire doivent inclure l’en-tête Content-Disposition, et le paramètre `name` doit être défini sur « knowledgeRequest ». Pour plus d’informations sur l’objet `imageInfo`, voir la [requête](#the-request).

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Les exemples de cet article montrent comment utiliser le jeton insights. Vous obtenez le jeton insights à partir d’un objet image dans une réponse de l’API /images/search. Pour plus d’informations sur l’obtention du jeton insights, consultez la section [API Recherche d’images Bing](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```


Pour obtenir des exemples qui utilisent le jeton insights, consultez [C#](#using-csharp) | [Java](#using-java) | [Node.js](#using-nodejs) | [Python](#using-python).

<a name="using-csharp" />

## <a name="using-c"></a>Utilisation de C#

### <a name="prerequisites"></a>Prérequis

Vous avez besoin de [Visual Studio 2017](https://www.visualstudio.com/downloads/) pour que ce code s’exécute sous Windows. (La version Community Edition gratuite fonctionne.)

Pour ce démarrage rapide, vous pouvez utiliser une clé d’abonnement en [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou en version payante.

## <a name="running-the-application"></a>Exécution de l'application

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Créez une solution de console dans Visual Studio.
1. Remplacez le contenu de `Program.cs` par le code affiché dans ce démarrage rapide.
2. Remplacez la valeur `accessKey` par votre clé d’abonnement.
2. Remplacez la valeur `insightsToken` par un jeton insights provenant d’une réponse /images/search.
3. Exécutez le programme.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
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

<a name="using-java" />

## <a name="using-java"></a>Utilisation de Java

### <a name="prerequisites"></a>Prérequis

Pour compiler et exécuter ce code, vous devez disposer de [JDK 7 ou 8](https://aka.ms/azure-jdks). Vous pouvez utiliser un IDE Java si vous le souhaitez, mais un éditeur de texte est suffisant.

Pour ce démarrage rapide, vous pouvez utiliser une clé d’abonnement en [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou en version payante.

## <a name="running-the-application"></a>Exécution de l'application

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Téléchargez ou installez la [bibliothèque gson](https://github.com/google/gson). Vous pouvez également l’obtenir via Maven.
2. Créez un projet Java dans votre éditeur ou IDE favori.
3. Ajoutez le code fourni dans un fichier nommé `VisualSearch.java`.
4. Remplacez la valeur `subscriptionKey` par votre clé d’abonnement.
5. Exécutez le programme.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    
}
```


<a name="using-nodejs" />

## <a name="using-nodejs"></a>Utilisation de Node.js

### <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Node.js 6](https://nodejs.org/en/download/) pour exécuter ce code.

Pour ce démarrage rapide, vous pouvez utiliser une clé d’abonnement en [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou en version payante.

## <a name="running-the-application"></a>Exécution de l'application

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Créez un dossier pour votre projet (ou utilisez votre éditeur ou IDE préféré).
2. À partir d’un terminal ou d’une invite de commandes, accédez au dossier que vous venez de créer.
3. Installez les modules request :  
  ```  
  npm install request  
  ```  
3. Installez les modules form-data :  
  ```  
  npm install form-data  
  ```  
4. Créez un fichier nommé GetVisualInsights.js et ajoutez le code suivant à ce dernier.
5. Remplacez la valeur `subscriptionKey` par votre clé d’abonnement.
7. Exécutez le programme.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


<a name="using-python" />

## <a name="using-python"></a>Utilisation de Python


### <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Python 3](https://www.python.org/) pour exécuter ce code.

Pour ce démarrage rapide, vous pouvez utiliser une clé d’abonnement en [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou en version payante.

## <a name="running-the-walkthrough"></a>Exécution de la procédure pas à pas

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Créez un projet Python dans votre IDE ou votre éditeur favori.
2. Créez un fichier nommé visualsearch.py et ajoutez le code indiqué dans ce démarrage rapide.
3. Remplacez la valeur `SUBSCRIPTION_KEY` par votre clé d’abonnement.
4. Exécutez le programme.


```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel de l’application à page unique Recherche visuelle Bing](tutorial-bing-visual-search-single-page-app.md)  
[Présentation de l’API Recherche visuelle Bing](overview.md)  
[Essayer](https://aka.ms/bingvisualsearchtryforfree)  
[Obtenir une clé d’accès d’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informations de référence sur l’API Recherche visuelle Bing](https://aka.ms/bingvisualsearchreferencedoc)
