---
title: 'Démarrage rapide : Convertir un script de texte, C# - Traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez convertir le texte d’un script d’une langue à une autre à l’aide de l’API de traduction de texte Translator Text avec C#.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: erhopf
ms.openlocfilehash: d0dd7ef04f6feb04df4fafc5b750f291d05f2862
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646206"
---
# <a name="quickstart-transliterate-text-with-the-translator-text-rest-api-c"></a>Démarrage rapide : Translittérer du texte à l’aide de l’API REST de traduction de texte Translator Text (C#)

Dans ce démarrage rapide, vous convertissez du texte dans la langue d’un script vers une autre à l’aide de l’API de traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Visual Studio 2017](https://www.visualstudio.com/downloads/) pour exécuter ce code sur Windows. (La version Community Edition gratuite fonctionne.)

Pour utiliser l’API de traduction de texte Translator Text, vous avez également besoin d’une clé d’abonnement. Consultez [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Comment s’inscrire à l’API de traduction de texte Translator Text).

## <a name="transliterate-request"></a>Requête Translittérer

> [!TIP]
> Obtenez le code le plus récent à partir de [Github](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

Ce qui suit convertit le texte dans la langue d’un script vers une autre en utilisant la méthode [Translittérer](./reference/v3-0-transliterate.md).

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
        static string path = "/transliterate?api-version=3.0";
        // Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
        static string params_ = "&language=ja&fromScript=jpan&toScript=latn";

        static string uri = host + path + params_;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // Transliterate "good afternoon".
        static string text = "こんにちは";

        async static void Transliterate()
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
            Transliterate();
            Console.ReadLine();
        }
    }
}
```

## <a name="transliterate-response"></a>Réponse Translittérer

Une réponse correcte est renvoyée au format JSON, comme dans l’exemple suivant :

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez l’exemple de code pour ce démarrage rapide et d’autres, y compris la traduction et l’identification de la langue, ainsi que d’autres exemples de projets de l’API de traduction de texte Translator Text sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)
