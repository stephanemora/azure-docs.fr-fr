---
title: 'Démarrage rapide : Convertir un script de texte, C# - Traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à translittérer (convertir) le texte d’un script dans un autre à l’aide de .NET Core et de l’API REST de traduction de texte Translator Text. Dans cet exemple, le japonais est translittéré pour utiliser l’alphabet latin.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 0c36830709c59632945a8c11e8a7113dcd5dc7e7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704564"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-c"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text et C# pour translittérer du texte

Dans ce guide de démarrage rapide, vous allez apprendre à translittérer (convertir) le texte d’un script dans un autre à l’aide de .NET Core (C#), de C# 7.1 ou version ultérieure et de l’API REST de traduction Translator Text. Dans l’exemple fourni, le japonais est translittéré pour utiliser l’alphabet latin.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

* C# 7.1 ou version ultérieure
* [Kit de développement logiciel (SDK) .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Package NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-net-core-project"></a>Créer un projet .NET Core

Ouvrez une nouvelle invite de commandes (ou une session Terminal Server) et exécutez les commandes suivantes :

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

La première commande effectue deux opérations. Elle crée une application console .NET et un répertoire nommé `transliterate-sample`. La deuxième commande bascule vers le répertoire de votre projet.

Ensuite, vous devez installer Json.Net. À partir du répertoire de votre projet, exécutez :

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Sélectionner la version du langage C#

Ce démarrage rapide requiert C# 7.1 ou version ultérieure. Il existe plusieurs façons de changer la version C# de votre projet. Dans ce guide, nous allons vous montrer comment ajuster le fichier `transliterate-sample.csproj`. Pour toutes les options disponibles, comme la modification du langage dans Visual Studio, consultez [Sélectionner la version du langage C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Ouvrez votre projet, puis ouvrez `transliterate-sample.csproj`. Vérifiez que `LangVersion` est défini sur 7.1 ou version ultérieure. En l’absence d’un groupe de propriétés pour la version du langage, ajoutez ces lignes :

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Ajouter les espaces de noms requis à votre projet

La commande `dotnet new console` que vous avez exécutée précédemment a créé un projet, notamment `Program.cs`. C’est dans ce fichier que vous placerez votre code d’application. Ouvrez `Program.cs` et remplacez les instructions using existantes. Ces instructions garantissent que vous avez accès à tous les types nécessaires pour générer et exécuter l’exemple d’application.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Créer des classes pour la réponse JSON

Ensuite, nous allons créer une classe qui est utilisée lors de la désérialisation de la réponse JSON retournée par l’API Translator Text.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TransliterationResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}
```

## <a name="create-a-function-to-transliterate-text"></a>Créer une fonction pour translittérer du texte

Dans la classe `Program`, créez une fonction asynchrone appelée `TransliterateTextRequest()`. Cette fonction prend quatre arguments : `subscriptionKey`, `host`, `route` et `inputText`.

```csharp
static public async Task TransliterateTextRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Sérialiser la demande de traduction

Ensuite, nous devons créer et sérialiser l’objet JSON qui inclut le texte à traduire. N’oubliez pas que vous pouvez passer plusieurs objets dans le `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
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
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TransliterationResult[] deserializedOutput = JsonConvert.DeserializeObject<TransliterationResult[]>(result);
// Iterate over the deserialized results.
foreach (TransliterationResult o in deserializedOutput)
{
    Console.WriteLine("Transliterated to {0} script: {1}", o.Script, o.Text);
}
```

Si vous utilisez un abonnement multiservice Cognitive Services, vous devez également inclure la `Ocp-Apim-Subscription-Region` dans vos paramètres de requête. [En savoir plus sur l’authentification sur l’abonnement multiservice](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Assemblage

La dernière étape consiste à appeler `TransliterateTextRequest()` dans la fonction `Main`. Dans cet exemple, nous allons translittérer le japonais pour utiliser l’alphabet latin. Recherchez `static void Main(string[] args)` et remplacez-le par ce code :

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
    string textToTransliterate = @"こんにちは";
    await TransliterateTextRequest(subscriptionKey, host, route, textToTransliterate);
}
```

Vous remarquerez que dans `Main`, vous déclarez `subscriptionKey`, `host`, `route` et le script de translittération de `textToTransliterate`.

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
dotnet run
```

## <a name="sample-response"></a>Exemple de réponse

Après avoir exécuté l’exemple, vous devriez voir ce qui suit sur le terminal :

```bash
Transliterated to latn script: Kon\'nichiwa
```

Ce message est généré à partir de JSON brut, qui se présente comme suit :

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

Consultez les informations de référence sur l’API pour comprendre tout ce que vous pouvez faire avec l’API de traduction de texte Translator Text.

> [!div class="nextstepaction"]
> [Informations de référence sur l'API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Voir aussi

* [Traduire le texte](quickstart-csharp-translate.md)
* [Identifier la langue par entrée](quickstart-csharp-detect.md)
* [Obtenir des traductions alternatives](quickstart-csharp-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-csharp-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-csharp-sentences.md)
