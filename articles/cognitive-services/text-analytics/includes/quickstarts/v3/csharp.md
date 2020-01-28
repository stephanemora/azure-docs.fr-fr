---
title: 'Démarrage rapide : Bibliothèque cliente Analyse de texte v3 pour C# | Microsoft Docs'
description: Découvrez comment utiliser la bibliothèque cliente Analyse de texte v3 pour C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2cf9a006e1f6f1edb996aa9beaf8934a14af29df
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281147"
---
<a name="HOLTop"></a>

[Documentation de référence](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Exemples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

> [!NOTE]
> * Ce guide de démarrage rapide utilise la version `3.0-preview` de la bibliothèque cliente Analyse de texte, qui comprend une préversion publique des fonctionnalités [Analyse des sentiments](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) et [Reconnaissance d’entité nommée (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) améliorées.
> * Le code indiqué dans cet article utilise des méthodes synchrones et un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Pour les scénarios de production, nous vous recommandons d’utiliser les méthodes asynchrones par lots afin d’optimiser les performances et l’extensibilité. Par exemple, l’appel de `AnalyzeSentimentAsync()` au lieu de `AnalyzeSentiment()`.

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* [IDE Visual Studio](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Créez une application .NET Core

En utilisant l’IDE Visual Studio, créez une application console .NET Core. Cette action va créer un projet nommé « Hello World » avec un seul fichier source C# : *program.cs*.

Installez la bibliothèque cliente en cliquant avec le bouton droit sur la solution dans l’**Explorateur de solutions** et en sélectionnant **Gérer les packages NuGet**. Dans le gestionnaire de package qui s’ouvre, sélectionnez **Parcourir**, cochez **Inclure la préversion** et recherchez `Azure.AI.TextAnalytics`. Cliquez dessus, puis sur **Installer**. Vous pouvez aussi utiliser la [Console du Gestionnaire de package](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Ouvrez le fichier *program.cs* et ajoutez les directives `using` suivantes :

```csharp
using System;
using Azure.AI.TextAnalytics;
```

Dans la classe `Program` de l’application, créez des variables pour la clé et le point de terminaison de votre ressource.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Remplacez la méthode `Main` de l’application. Vous définirez les méthodes appelées ici ultérieurement.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);

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

## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet `TextAnalyticsClient` qui s’authentifie auprès d’Azure avec votre clé et fournit des fonctions permettant d’accepter du texte sous forme de chaînes individuelles ou de lots. Vous pouvez envoyer du texte à l’API de façon synchrone ou de façon asynchrone. L’objet Response contient les informations d’analyse de chaque document que vous envoyez. Une instance facultative de `TextAnalyticsClientOptions` peut être utilisée pour lancer le client avec différents paramètres par défaut (par exemple, la langue par défaut ou l’indication de pays).

Vous trouverez des exemples supplémentaires, notamment l’authentification AAD et l’utilisation des paramètres par défaut du client, [ici](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples).

## <a name="code-examples"></a>Exemples de code

* [Analyse des sentiments](#sentiment-analysis) (préversion publique)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entité nommée](#named-entity-recognition-public-preview) (préversion publique)
* [Reconnaissance d’entité nommée : informations personnelles](#named-entity-recognition---personal-information-public-preview) (préversion publique)
* [Liaison d’entités](#entity-linking)
* [Extraction de phrases clés](#key-phrase-extraction)

Dans la méthode `main()` de votre programme, appelez la méthode d’authentification pour instancier le client.

## <a name="sentiment-analysis-public-preview"></a>Analyse des sentiments (préversion publique)

> [!NOTE]
> Le code ci-dessous est destiné à Analyse des sentiments v3, disponible en préversion publique.

Créez une fonction appelée `SentimentAnalysisExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `AnalyzeSentiment()`. L’objet `Response<AnalyzeSentimentResult>` retourné contient l’étiquette de sentiment et le score de l’intégralité du document d’entrée ainsi qu’une analyse des sentiments pour chaque phrase si l’opération réussit et un `Value.ErrorMessage` si ce n’est pas le cas.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

## <a name="language-detection"></a>Détection de la langue

Créez une fonction appelée `LanguageDetectionExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `DetectLanguage()`. L’objet `Response<DetectLanguageResult>` retourné contient la langue détectée dans `Value.PrimaryLanguage` si l’opération réussit et un `Value.ErrorMessage` si ce n’est pas le cas.

> [!Tip]
> Dans certains cas, il peut être difficile de lever toute ambiguïté sur les langues en fonction de l’entrée. Vous pouvez utiliser le paramètre `countryHint` pour spécifier un code de pays à 2 lettres. L’API utilise « US » comme countryHint par défaut. Pour modifier ce comportement, vous pouvez réinitialiser ce paramètre en définissant cette valeur sur une chaîne vide `countryHint = ""`. Pour définir une autre valeur par défaut, définissez la propriété `TextAnalyticsClientOptions.DefaultCountryHint` et transmettez-la pendant l’initialisation du client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

## <a name="named-entity-recognition-public-preview"></a>Reconnaissance d’entité nommée (préversion publique)

> [!NOTE]
> Le code ci-dessous concerne Reconnaissance d’entité nommée v3, disponible en préversion publique.

Créez une fonction appelée `EntityRecognitionExample()` qui accepte le client que vous avez créé, appelez sa fonction `RecognizeEntities()`, puis effectuez une itération dans les résultats. L’objet `Response<RecognizeEntitiesResult>` retourné contient la liste des entités détectées dans `Value.NamedEntities` si l’opération réussit, et `Value.ErrorMessage` si elle échoue. Pour chaque entité détectée, affichez son type et sous-type, le cas échéant.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Reconnaissance d’entité nommée : informations personnelles (préversion publique)

> [!NOTE]
> Le code ci-dessous concerne la détection d’informations personnelles à l’aide de Reconnaissance d’entité nommée v3, disponible en préversion publique.

Créez une fonction appelée `EntityPIIExample()` qui accepte le client que vous avez créé, appelez sa fonction `RecognizePiiEntities()`, puis effectuez une itération dans les résultats. À l’image de la fonction précédente, l’objet `Response<RecognizeEntitiesResult>` retourné contient la liste des entités détectées dans `Value.NamedEntities` si l’opération réussit, et `Value.ErrorMessage` si elle échoue.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```

## <a name="entity-linking"></a>Liaison d’entités

Créez une fonction appelée `EntityLinkingExample()` qui accepte le client que vous avez créé, appelez sa fonction `RecognizeLinkedEntities()`, puis effectuez une itération dans les résultats. L’objet `Response<RecognizeLinkedEntitiesResult>` retourné contient la liste des entités détectées dans `Value.LinkedEntities` si l’opération réussit, et `Value.ErrorMessage` si elle échoue. Les entités liées étant identifiées de manière unique, les occurrences d’une même entité sont regroupées sous un objet `LinkedEntity` sous la forme d’une liste d’objets `LinkedEntityMatch`.

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Créez une fonction appelée `KeyPhraseExtractionExample()` qui accepte le client que vous avez créé, puis appelez sa fonction `ExtractKeyPhrases()`. Le résultat contiendra la liste des expressions clés détectées dans `Value.KeyPhrases` si l’opération réussit et un `Value.ErrorMessage` si ce n’est pas le cas. Imprimez les expressions clés détectées.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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
