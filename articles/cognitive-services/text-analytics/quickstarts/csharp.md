---
title: 'Démarrage rapide : Appeler le service Analyse de texte à l’aide du SDK Azure pour .NET et C#'
titleSuffix: Azure Cognitive Services
description: Informations et exemples de code pour commencer à utiliser le service Analyse de texte et C#.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356966"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Démarrage rapide : Utiliser le SDK .NET et C# pour appeler le service Analyse de texte
<a name="HOLTop"></a>

Ce guide de démarrage rapide vous aide à commencer à utiliser le SDK Azure pour .NET et C# pour analyser la langue. Si l’API REST [Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759711) est compatible avec la plupart des langages de programmation, le kit SDK offre quant à lui un moyen facile d’intégrer le service à vos applications.

> [!NOTE]
> Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Pour les détails techniques, reportez-vous au SDK pour consulter la [référence Analyse de texte](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet) .NET.

## <a name="prerequisites"></a>Prérequis

* N’importe quelle édition de [visual studio 2017 ou ultérieure]
* [SDK Analyse de texte pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Il vous faut également le [point de terminaison et la clé d’accès](../How-tos/text-analytics-how-to-access-key.md) générée pendant le processus d’inscription.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Créer la solution Visual Studio et installer le SDK

1. Créez un projet Application de console (.NET Core). [Accédez à Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Cliquez avec le bouton droit sur la solution et sélectionnez **Gérer les packages NuGet pour la solution**.
1. Sélectionnez l’onglet **Parcourir**. Recherchez **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

## <a name="authenticate-your-credentials"></a>S’authentifier avec vos informations d’identification

1. Ajoutez les instructions `using` suivantes au fichier de classe principal (Program.cs par défaut).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Créez une classe `ApiKeyServiceClientCredentials` pour stocker les informations d’identification et les ajouter à chaque demande.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Modifiez la classe `Program`. Ajoutez un membre de constante pour votre clé API Analyse de texte et un autre pour le point de terminaison de service. Pensez à utiliser l’emplacement Azure correct pour votre ressource Analyse de texte.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Pour renforcer la sécurité des secrets dans des systèmes de production, nous recommandons d’utiliser [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Créer un client Analyse de texte

Dans la fonction `Main` de votre projet, appelez la méthode d’exemple de votre choix. Transmettez les paramètres `Endpoint` et `ApiKey` que vous avez définis.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

Les sections suivantes expliquent comment appeler chacune des fonctionnalités du service.

## <a name="perform-sentiment-analysis"></a>Exécuter une analyse des sentiments

1. Créez une fonction `SentimentAnalysisExample()` qui sélectionne le client créé précédemment.
2. Générez une liste d’objets `MultiLanguageInput` contenant les documents que vous voulez analyser.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. Dans la même fonction, appelez `client.SentimentAsync()` et affichez le résultat. Effectuez ensuite une itération dans les résultats. Imprimez l’ID et le score de sentiment de chaque document. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Exécuter une détection de la langue

1. Créez une fonction `DetectLanguageExample()` qui sélectionne le client créé précédemment.
2. Générez une liste d’objets `LanguageInput` contenant vos documents.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. Dans la même fonction, appelez `client.DetectLanguageAsync()` et affichez le résultat. Effectuez ensuite une itération dans les résultats. Imprimez l’ID de chaque document et la première langue retournée.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
```

## <a name="perform-entity-recognition"></a>Exécuter une reconnaissance d’entité

1. Créez une fonction `RecognizeEntitiesExample()` qui sélectionne le client créé précédemment.
2. Générez une liste d’objets `MultiLanguageBatchInput` contenant vos documents.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. Dans la même fonction, appelez `client.EntitiesAsync()` et affichez le résultat. Effectuez ensuite une itération dans les résultats. Imprimez l’ID de chaque document. Pour chaque entité détectée, imprimez son nom Wikipédia, le type et les sous-types (le cas échéant), ainsi que les emplacements dans le texte d’origine.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>Exécuter une extraction d’expressions clés

1. Créez une fonction `KeyPhraseExtractionExample()` qui sélectionne le client créé précédemment.
2. Générez une liste d’objets `MultiLanguageBatchInput` contenant vos documents.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. Dans la même fonction, appelez `client.KeyPhrasesAsync()` et affichez le résultat. Effectuez ensuite une itération dans les résultats. Imprimez l’ID de chaque document et les expressions clés détectées.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                cat
                veterinarian
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
