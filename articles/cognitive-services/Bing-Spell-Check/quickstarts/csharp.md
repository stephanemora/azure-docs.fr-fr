---
title: 'Démarrage rapide : API Vérification orthographique Bing, C#'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Vérification orthographique Bing.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: e723bc117cff208ea824dae216b8e8b57f7f26d7
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801452"
---
# <a name="quickstart-for-bing-spell-check-api-with-c"></a>Démarrage rapide pour l’API Vérification orthographique Bing avec C#

Cet article vous explique comment utiliser l’[API Vérification orthographique Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) avec C#. L’API Vérification orthographique renvoie une liste de mots qu’elle ne reconnaît pas avec des suggestions de remplacements. Généralement, vous envoyez du texte à cette API, puis vous effectuez les remplacements suggérés dans le texte ou vous les montrez à l’utilisateur de votre application pour qu’il puisse décider de les effectuer ou non. Cet article explique comment envoyer une requête contenant le texte « Hollo, wrld! » Les remplacements suggérés sont « Hello » et « world ».

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Visual Studio 2017](https://www.visualstudio.com/downloads/) pour exécuter ce code sur Windows. (La version Community Edition gratuite fonctionne.)

Vous devez disposer d’un [compte API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec **API vérification orthographique Bing v7**. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/#lang) est suffisant pour suivre ce guide de démarrage rapide. Vous aurez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou de la clé d’un abonnement payant présente sur votre tableau de bord Azure.

## <a name="get-spell-check-results"></a>Obtenir les résultats de la vérification orthographique

1. Créez un projet C# dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `subscriptionKey` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;

namespace SpellCheckSample1
{
    class Program
    {
        static string host = "https://api.cognitive.microsoft.com";
        static string path = "/bing/v7.0/spellcheck?";

        // For a list of available markets, go to:
        // https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#market-codes
        static string params_ = "mkt=en-US&mode=proof";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "Hollo, wrld!";

        // These properties are used for optional headers (see below).
        // static string ClientId = "<Client ID from Previous Response Goes Here>";
        //static string ClientId = "2325577A61966D252A475CD760C96C03";
        // static string ClientIp = "999.999.999.999";
        // static string ClientLocation = "+90.0000000000000;long: 00.0000000000000;re:100.000000000000";

        async static void SpellCheck()
        {
            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            // The following headers are optional, but it is recommended they be treated as required.
            // These headers help the service return more accurate results.
            //client.DefaultRequestHeaders.Add("X-Search-Location", ClientLocation);
            //client.DefaultRequestHeaders.Add("X-MSEdge-ClientID", ClientId);
            //client.DefaultRequestHeaders.Add("X-MSEdge-ClientIP", ClientIp);

            HttpResponseMessage response = new HttpResponseMessage();
            string uri = host + path + params_;

            List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
            values.Add(new KeyValuePair<string, string>("text", text));

            using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
            {
                content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
                response = await client.PostAsync(uri, content);
            }

            string client_id;
            if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
            {
                client_id = header_values.First();
                Console.WriteLine("Client ID: " + client_id);
            }

            string contentString = await response.Content.ReadAsStringAsync();
            Console.WriteLine(JsonPrettyPrint(contentString));

        }

        static void Main(string[] args)
        {
            SpellCheck();
            Console.ReadLine();
        }

        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
            {
                return string.Empty;
            }

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 3;

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

**Réponse**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel de l’API Vérification orthographique Bing](../tutorials/spellcheck.md)

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de l’API Vérification orthographique Bing](../proof-text.md)
- [Informations de référence sur l’API Vérification orthographique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
