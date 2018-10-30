---
title: 'Démarrage rapide : Obtenir la longueur des phrases, C# - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez rechercher la longueur des phrases du texte à l’aide de l’API de traduction de texte Translator Text avec C#.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: erhopf
ms.openlocfilehash: c10a38164c71eaa4239072fe10973932ce8cce3b
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645020"
---
# <a name="quickstart-get-sentence-lengths-with-the-translator-text-rest-api-c"></a>Démarrage rapide : Obtenir la longueur des phrases avec l’API REST de traduction de texte Translator Text (C#)

Dans ce démarrage rapide, vous allez rechercher les longueurs des phrases dans le texte à l’aide de l’API de traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Visual Studio 2017](https://www.visualstudio.com/downloads/) pour exécuter ce code sur Windows. (La version Community Edition gratuite fonctionne.)

Pour utiliser l’API de traduction de texte Translator Text, vous avez également besoin d’une clé d’abonnement. Consultez [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Comment s’inscrire à l’API de traduction de texte Translator Text).

## <a name="breaksentence-request"></a>Requête BreakSentence

> [!TIP]
> Obtenez le code le plus récent à partir de [Github](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

Le code suivant fractionne le texte source en phrases à l’aide de la méthode [BreakSentence](./reference/v3-0-break-sentence.md).

1. Créez un projet C# dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/breaksentence?api-version=3.0";

        static string uri = host + path;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "How are you? I am fine. What did you do today?";

        async static void Break()
        {
            System.Object[] body = new System.Object[] { new { Text = text } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Break();
            Console.ReadLine();
        }
    }
}
```

## <a name="breaksentence-response"></a>Réponse BreakSentence

Une réponse correcte est renvoyée au format JSON, comme dans l’exemple suivant :

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez l’exemple de code pour ce démarrage rapide et d’autres, y compris la traduction et la translittération, ainsi que d’autres exemples de projets de l’API de traduction de texte Translator Text sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)
