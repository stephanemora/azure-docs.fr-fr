---
title: 'Démarrage rapide : Bibliothèque de client Analyse de texte pour C# | Microsoft Docs'
description: Découvrir comment utiliser la bibliothèque de client Analyse de texte pour C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 64eb19e43223c1953a7244f8fd29c48d085f1e96
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80117218"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

[Documentation de référence v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Code source de la bibliothèque v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Package v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Exemples v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Documentation de référence v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Code source de la bibliothèque v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Package v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Exemples v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* [IDE Visual Studio](https://visualstudio.microsoft.com/vs/)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="créez une ressource Analyse de texte"  target="_blank">Créer une ressource Analyse de texte <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison.  Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Analyse de texte. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-net-core-application"></a>Créez une application .NET Core

En utilisant l’IDE Visual Studio, créez une application console .NET Core. Cette action va créer un projet nommé « Hello World » avec un seul fichier source C# : *program.cs*.

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Installez la bibliothèque cliente en cliquant avec le bouton droit sur la solution dans l’**Explorateur de solutions** et en sélectionnant **Gérer les packages NuGet**. Dans le gestionnaire de package qui s’ouvre, sélectionnez **Parcourir**, cochez **Inclure la préversion** et recherchez `Azure.AI.TextAnalytics`. Sélectionnez la version `1.0.0-preview.3`, puis **Installer**. Vous pouvez aussi utiliser la [Console du Gestionnaire de package](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez [sur GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), qui contient les exemples de code de ce guide de démarrage rapide. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Installez la bibliothèque cliente en cliquant avec le bouton droit sur la solution dans l’**Explorateur de solutions** et en sélectionnant **Gérer les packages NuGet**. Dans le gestionnaire de package qui s’ouvre, sélectionnez **Parcourir** et recherchez `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Cliquez dessus, puis sur **Installer**. Vous pouvez aussi utiliser la [Console du Gestionnaire de package](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez [sur GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), qui contient les exemples de code dans ce guide de démarrage rapide. 

---

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Ouvrez le fichier *program.cs* et ajoutez les directives `using` suivantes :

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Dans la classe `Program` de l’application, créez des variables pour la clé et le point de terminaison de votre ressource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Remplacez la méthode `Main` de l’application. Vous définirez les méthodes appelées ici ultérieurement.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Ouvrez le fichier *program.cs* et ajoutez les directives `using` suivantes :

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Dans la classe `Program` de l’application, créez des variables pour la clé et le point de terminaison de votre ressource. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Remplacez la méthode `Main` de l’application. Vous définirez les méthodes appelées ici ultérieurement.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet `TextAnalyticsClient` qui s’authentifie auprès d’Azure avec votre clé et fournit des fonctions permettant d’accepter du texte sous forme de chaînes individuelles ou de lots. Vous pouvez envoyer du texte à l’API de façon synchrone ou de façon asynchrone. L’objet Response contient les informations d’analyse de chaque document que vous envoyez. 

Si vous utilisez la version `3.0-preview`, vous pouvez utiliser une instance facultative de `TextAnalyticsClientOptions` pour initialiser le client avec différents paramètres par défaut (par exemple, la langue par défaut ou l’indicateur de pays). Vous pouvez également vous authentifier à l’aide d’un jeton Azure Active Directory. 

## <a name="code-examples"></a>Exemples de code

* [Analyse des sentiments](#sentiment-analysis)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entité nommée](#named-entity-recognition-ner)
* [Détection d’informations personnelles](#detect-personal-information)
* [Liaison d’entités](#entity-linking)
* [Extraction de phrases clés](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Authentifier le client

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Vérifiez que votre méthode principale précédemment utilisée crée un objet client avec votre point de terminaison et vos informations d’identification.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Créez une classe `ApiKeyServiceClientCredentials` pour stocker les informations d’identification et les ajouter aux requêtes du client. Dans cette classe, créez une substitution pour `ProcessHttpRequestAsync()` qui ajoute votre clé à l’en-tête `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Créez une méthode pour instancier l’objet [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) avec votre point de terminaison et un objet `ApiKeyServiceClientCredentials` contenant votre clé.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>analyse de sentiments

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Créez une fonction appelée `SentimentAnalysisExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `AnalyzeSentiment()`. L’objet `Response<DocumentSentiment>` retourné contient l’étiquette de sentiment et le score de l’intégralité du document d’entrée ainsi qu’une analyse des sentiments pour chaque phrase si l’opération réussit. En cas d’erreur, une exception `RequestFailedException`est levée.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Créez une nouvelle fonction appelée `SentimentAnalysisExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet). L’objet [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) retourné contiendra le sentiment `Score` si l’opération réussit, et `errorMessage` si elle échoue. 

Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Détection de la langue

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)


Créez une fonction appelée `LanguageDetectionExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `DetectLanguage()`. L’objet `Response<DetectedLanguage>` retourné contient la langue détectée ainsi que son nom et le code ISO-6391. En cas d’erreur, une exception `RequestFailedException` est levée.

> [!Tip]
> Dans certains cas, il peut être difficile de lever toute ambiguïté sur les langues en fonction de l’entrée. Vous pouvez utiliser le paramètre `countryHint` pour spécifier un code de pays à 2 lettres. L’API utilise « US » comme countryHint par défaut. Pour modifier ce comportement, vous pouvez réinitialiser ce paramètre en définissant cette valeur sur une chaîne vide `countryHint = ""`. Pour définir une autre valeur par défaut, définissez la propriété `TextAnalyticsClientOptions.DefaultCountryHint` et transmettez-la pendant l’initialisation du client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Créez une nouvelle fonction appelée `languageDetectionExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). L’objet [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) retourné contiendra la liste des langues détectées dans `DetectedLanguages` si l’opération réussit, et `errorMessage` si elle échoue. Imprimez la première langue retournée.

> [!Tip]
> Dans certains cas, il peut être difficile de lever toute ambiguïté sur les langues en fonction de l’entrée. Vous pouvez utiliser le paramètre `countryHint` pour spécifier un code de pays à 2 lettres. L’API utilise « US » comme countryHint par défaut. Pour modifier ce comportement, vous pouvez réinitialiser ce paramètre en définissant cette valeur sur une chaîne vide `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Output

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Reconnaissance d’entité nommée (NER)

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)


> [!NOTE]
> Nouveautés de la version `3.0-preview` :
> * La reconnaissance d’entités offre désormais la possibilité de détecter des informations personnelles dans du texte.
> * La liaison d’entités est désormais distincte de la reconnaissance d’entités.


Créez une fonction appelée `EntityRecognitionExample()` qui accepte le client que vous avez créé, appelez sa fonction `RecognizeEntities()`, puis effectuez une itération dans les résultats. L’objet `Response<IReadOnlyCollection<CategorizedEntity>>` retourné contient la liste des entités détectées. En cas d’erreur, une exception `RequestFailedException` est levée.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="detect-personal-information"></a>Détection d’informations personnelles

Créez une fonction appelée `EntityPIIExample()` qui accepte le client que vous avez créé, appelez sa fonction `RecognizePiiEntities()`, puis effectuez une itération dans les résultats. À l’image de la fonction précédente, l’objet `Response<IReadOnlyCollection<CategorizedEntity>>` retourné contient la liste des entités détectées. En cas d’erreur, une exception `RequestFailedException` est levée.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category:
                Length: 11,     Score: 0.85
```


## <a name="entity-linking"></a>Liaison d’entités

Créez une fonction appelée `EntityLinkingExample()` qui accepte le client que vous avez créé, appelez sa fonction `RecognizeLinkedEntities()`, puis effectuez une itération dans les résultats. L’objet `Response<IReadOnlyCollection<LinkedEntity>>` retourné représente la liste des entités détectées. En cas d’erreur, une exception `RequestFailedException` est levée. Les entités liées étant identifiées de manière unique, les occurrences d’une même entité sont regroupées sous un objet `LinkedEntity` sous la forme d’une liste d’objets `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

> [!NOTE]
> Dans la version 2.1, la liaison d’entités est incluse dans la réponse NER.

Créez une nouvelle fonction appelée `RecognizeEntitiesExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Effectuez une itération dans les résultats. L’objet [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) retourné contiendra la liste des entités détectées dans `Entities` si l’opération réussit, et `errorMessage` si elle échoue. Pour chaque entité détectée, imprimez son type, son sous-type, son nom Wikipédia (s’il existe), ainsi que les emplacements dans le texte d’origine.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Créez une fonction appelée `KeyPhraseExtractionExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `ExtractKeyPhrases()`. L’objet `<Response<IReadOnlyCollection<string>>` retourné contient la liste des phrases clés détectées. En cas d’erreur, une exception `RequestFailedException` est levée.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Créez une fonction appelée `KeyPhraseExtractionExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Le résultat contiendra la liste des expressions clés détectées dans `KeyPhrases` si l’opération réussit et un `errorMessage` si ce n’est pas le cas. Imprimez les expressions clés détectées.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

---
