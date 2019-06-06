---
title: 'Démarrage rapide : Obtenir la longueur des phrases, C# - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à déterminer la longueur de phrase à l’aide de .NET Core et de l’API de traduction de texte Translator Text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: e8940de90b925a1ca252de3cf75acd192531edeb
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514216"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text et C# pour déterminer la longueur de phrase

Dans ce guide de démarrage rapide, vous allez apprendre à déterminer les longueurs de phrases à l’aide de .NET Core et de l’API de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Package NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-net-core-project"></a>Créer un projet .NET Core

Ouvrez une nouvelle invite de commandes (ou une session Terminal Server) et exécutez les commandes suivantes :

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

La première commande effectue deux opérations. Elle crée une application console .NET et un répertoire nommé `sentences-sample`. La deuxième commande bascule vers le répertoire de votre projet.

Ensuite, vous devez installer Json.Net. À partir du répertoire de votre projet, exécutez :

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Ajouter des espaces de noms requis à votre projet

La commande `dotnet new console` que vous avez exécutée précédemment a créé un projet, notamment `Program.cs`. C’est dans ce fichier que vous placerez votre code d’application. Ouvrez `Program.cs` et remplacez les instructions using existantes. Ces instructions garantissent que vous avez accès à tous les types nécessaires pour générer et exécuter l’exemple d’application.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-determine-sentence-length"></a>Créer une fonction pour déterminer la longueur de phrase

Dans la classe `Program`, créez une fonction nommée `BreakSentence`. Cette classe encapsule le code utilisé pour appeler la ressource BreakSentence, et affiche le résultat sur la console.

```csharp
static void BreakSentence()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Définir la clé d’abonnement, le nom d’hôte et le chemin

Ajoutez ces lignes à la fonction `BreakSentence`. Vous remarquerez qu’avec `api-version`, vous pouvez définir la langue d’entrée. Dans cet exemple, il s’agit de l’anglais.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/breaksentence?api-version=3.0&language=en";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Ensuite, vous devez créer et sérialiser l’objet JSON qui inclut le texte. N’oubliez pas que vous pouvez passer plusieurs objets dans le tableau `body`.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"How are you? I am fine. What did you do today?" } };
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

La dernière étape consiste à appeler `BreakSentence()` dans la fonction `Main`. Recherchez `static void Main(string[] args)` et ajoutez ces lignes :

```csharp
BreakSentence();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
dotnet run
```

## <a name="sample-response"></a>Exemple de réponse

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

Explorez l’exemple de code pour ce démarrage rapide et d’autres, y compris la translittération et l’identification de la langue, ainsi que d’autres exemples de projets de l’API de traduction de texte Translator Text sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Voir aussi

* [Traduire le texte](quickstart-csharp-translate.md)
* [Translittérer du texte](quickstart-csharp-transliterate.md)
* [Identifier la langue par entrée](quickstart-csharp-detect.md)
* [Obtenir des traductions alternatives](quickstart-csharp-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-csharp-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-csharp-sentences.md)
