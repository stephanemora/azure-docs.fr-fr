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
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: d12f6b400b270c6ef631d9f503980efef1ae8458
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840384"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Démarrage rapide : Utiliser le SDK .NET et C# pour appeler le service Analyse de texte
<a name="HOLTop"></a>

Ce guide de démarrage rapide vous aide à commencer à utiliser le SDK Azure pour .NET et C# pour analyser la langue. Si l’API REST [Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759711) est compatible avec la plupart des langages de programmation, le kit SDK offre quant à lui un moyen facile d’intégrer le service à vos applications.

> [!NOTE]
> Le code de démonstration dans cet article utilise les méthodes synchrones du SDK .NET Analyse de texte pour plus de simplicité. Toutefois, pour les scénarios de production, nous recommandons d’utiliser les méthodes asynchrones par lots dans vos propres applications pour les rendre plus évolutives et réactives. Par exemple, vous pouvez utiliser `SentimentBatchAsync` au lieu de `Sentiment`.
>
> Une version asynchrone par lots de cet exemple se trouve sur [GitHub.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics)

Pour les détails techniques, reportez-vous au SDK pour consulter la [référence Analyse de texte](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet) .NET.

## <a name="prerequisites"></a>Prérequis

* N’importe quelle édition de Visual studio 2017 ou version ultérieure
* [SDK Analyse de texte pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

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
    // Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    // You can get the resource location from Azure Portal -> your TA resource -> Overview
    // There are two acceptable formats for the endpoint, both
    // require that you omit the `/text/analytics/<version>` suffix:
    // 1. A location based endpoint URL -
    //     "https://<your-location>.api.cognitive.microsoft.com";
    // 2. A resource name based endpoint URL -
    //     "https://<your-resource-name>.cognitiveservices.azure.com";
    private const string Endpoint = "enter-your-base-resource-endpoint-here";
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
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

Les sections suivantes expliquent comment appeler chacune des fonctionnalités du service.

## <a name="perform-sentiment-analysis"></a>Exécuter une analyse des sentiments

1. Créez une fonction `SentimentAnalysisExample()` qui sélectionne le client créé précédemment.
2. Dans la même fonction, appelez `client.Sentiment()` et affichez le résultat. Le résultat contiendra le sentiment `Score` si l’opération réussit et `errorMessage` si ce n’est pas le cas. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Exécuter une détection de la langue

1. Créez une fonction `DetectLanguageExample()` qui sélectionne le client créé précédemment.
2. Dans la même fonction, appelez `client.DetectLanguage()` et affichez le résultat. Le résultat contiendra la liste des langues détectées dans `DetectedLanguages` si l’opération réussit et un `errorMessage` si ce n’est pas le cas. Imprimez ensuite la première langue retournée.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> Dans certains cas, il peut être difficile de lever toute ambiguïté sur les langues en fonction de l’entrée. Vous pouvez utiliser le paramètre `countryHint` pour spécifier un code de pays à 2 lettres. L’API utilise « US » comme countryHint par défaut. Pour modifier ce comportement, vous pouvez réinitialiser ce paramètre en définissant cette valeur sur une chaîne vide `countryHint = ""`.

### <a name="output"></a>Output

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Exécuter une reconnaissance d’entité

1. Créez une fonction `RecognizeEntitiesExample()` qui sélectionne le client créé précédemment.
2. Dans la même fonction, appelez `client.Entities()` et affichez le résultat. Effectuez ensuite une itération dans les résultats. Le résultat contiendra la liste des entités détectées dans `Entities` si l’opération réussit et un `errorMessage` si ce n’est pas le cas. Pour chaque entité détectée, imprimez son type, son sous-type, son nom Wikipédia (s’il existe), ainsi que les emplacements dans le texte d’origine.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Output

```console
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
2. Dans la même fonction, appelez `client.KeyPhrases()` et affichez le résultat. Le résultat contiendra la liste des expressions clés détectées dans `KeyPhrases` si l’opération réussit et un `errorMessage` si ce n’est pas le cas. Imprimez ensuite les expressions clés détectées.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
