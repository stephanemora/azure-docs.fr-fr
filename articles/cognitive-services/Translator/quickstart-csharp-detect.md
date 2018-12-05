---
title: 'Démarrage rapide : Identifier la langue du texte, C# - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à détecter la langue du texte fourni à l’aide de .NET Core et de l’API REST de traduction de texte Translator Text.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: erhopf
ms.openlocfilehash: 89aedb33a88a7fb5f0a4e0abed172ba4e1cd091e
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682064"
---
# <a name="quickstart-detect-text-language-with-the-translator-text-rest-api-c"></a>Démarrage rapide : Détecter la langue du texte avec l’API REST de traduction de texte Translator Text (C#)

Dans ce guide de démarrage rapide, vous allez apprendre à détecter la langue du texte fourni à l’aide de .NET Core et de l’API REST de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Package NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-net-core-project"></a>Créer un projet .NET Core

Ouvrez une nouvelle invite de commandes (ou une session Terminal Server) et exécutez les commandes suivantes :

```console
dotnet new console -o detect-sample
cd detect-sample
```

La première commande effectue deux opérations. Elle crée une application console .NET et un répertoire nommé `detect-sample`. La deuxième commande bascule vers le répertoire de votre projet.

Ensuite, vous devez installer Json.Net. À partir du répertoire de votre projet, exécutez :

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Ajouter des espaces de noms requis à votre projet

La commande `dotnet new console` que vous avez exécutée précédemment a créé un projet, y compris `Program.cs`. C’est dans ce fichier que vous placerez votre code d’application. Ouvrez `Program.cs` et remplacez les instructions using existantes. Ces instructions garantissent que vous avez accès à tous les types nécessaires pour générer et exécuter l’exemple d’application.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-detect-the-source-texts-language"></a>Créer une fonction pour détecter la langue du texte source

Dans la classe `Program`, créez une fonction nommée `Detect`. Cette classe encapsule le code utilisé pour appeler la ressource Detect, puis affiche le résultat sur la console.

```csharp
static void Detect()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Définir la clé d’abonnement, le nom d’hôte et le chemin

Ajoutez ces lignes à la fonction `Detect`.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/detect?api-version=3.0";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Ensuite, nous devons créer et sérialiser l’objet JSON qui inclut le texte qui sera soumis à la détection de langue.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Salve mondo!" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instancier le client et effectuer une requête

Ces lignes instancient `HttpClient` et `HttpRequestMessage` :

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construire la requête et imprimer la réponse

Dans `HttpRequestMessage`, vous allez :

* Déclarer la méthode HTTP.
* Construire l’URI de requête.
* Insérer le corps de la requête (objet JSON sérialisé).
* Ajouter des en-têtes obligatoires.
* Effectuer une requête asynchrone.
* Imprimer la réponse

Ajoutez ce code à `HttpRequestMessage` :

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Assemblage

La dernière étape consiste à appeler `Detect()` dans la fonction `Main`. Recherchez `static void Main(string[] args)` et ajoutez ces lignes :

```csharp
Detect();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session Terminal Server), accédez au répertoire de votre projet et exécutez :

```console
dotnet run
```

## <a name="sample-response"></a>Exemple de réponse

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

Explorez l’exemple de code pour ce démarrage rapide et d’autres, y compris la traduction et la translittération, ainsi que d’autres exemples de projets de l’API de traduction de texte Translator Text sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Voir aussi

* [Traduire le texte](quickstart-csharp-translate.md)
* [Translittérer du texte](quickstart-csharp-transliterate.md)
* [Obtenir des traductions alternatives](quickstart-csharp-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-csharp-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-csharp-sentences.md)
