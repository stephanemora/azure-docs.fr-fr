---
title: 'Démarrage rapide : URL Preview de projet, C#'
titlesuffix: Azure Cognitive Services
description: Prise en main de URL Preview de projet avec C#.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 8d31d3a83f9873ce550b9c78626eea0d96ac39bb
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867440"
---
# <a name="quickstart-url-preview-query-in-c"></a>Démarrage rapide : Requête d’aperçu d’URL dans C#

L’exemple C# suivant crée un aperçu d’Url pour le site web de SwiftKey : https://swiftkey.com/en.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Visual Studio 2017](https://www.visualstudio.com/downloads/) pour exécuter ce code sur Windows. (La version Community Edition gratuite fonctionne.)

Obtenir une clé d’accès pour l’essai gratuit [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scénario de code

Le code C# suivant crée un aperçu d’Url pour le site web de SwiftKey : https://swiftkey.com/en. 

Il est implémenté lors des étapes suivantes :
1. Déclarez les variables pour spécifier le point de terminaison et un aperçu d’URL de requête.  
2. Créez la requête.
3. Ajoutez l’en-tête *Ocp-Apim-Subscription-Key*. 
4. Exécutez la requête web de façon asynchrone. 
5. Lisez la réponse.
6. Imprimez les en-têtes et les résultats JSON dans la console.

**Code source**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
## <a name="running-the-application"></a>Exécution de l'application

Pour exécuter l’application :

1. Créez une solution de console dans Visual Studio.
2. Remplacez `Program.cs` par le code fourni.
3. Remplacez la valeur `YOUR-ACCESS-KEY` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide JavaScript](javascript.md)
- [Démarrage rapide Node](node-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)