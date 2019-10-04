---
title: 'Démarrage rapide : Bibliothèque de client Analyse de texte pour .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Suivez ce guide de démarrage rapide pour utiliser l’API Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: assafi
ms.openlocfilehash: 6e6f1d5cfe1f5e745e6f780b5cb9f979520a1f91
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70134929"
---
# <a name="quickstart-text-analytics-client-library-for-net"></a>Démarrage rapide : Bibliothèque de client Analyse de texte pour .NET
<a name="HOLTop"></a>

Découvrez comment utiliser la bibliothèque de client Analyse de texte pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. 

Utilisez la bibliothèque de client Analyse de texte pour .NET dans le cadre des opérations suivantes :

* analyse de sentiments
* Détection de la langue
* Reconnaissance d’entité
* Extraction d’expressions clés

[Documentation de référence](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Exemples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE] 
> Le code de démonstration dans cet article utilise les méthodes synchrones du SDK .NET Analyse de texte pour plus de simplicité. Toutefois, pour les scénarios de production, nous recommandons d’utiliser les méthodes asynchrones par lots dans vos propres applications pour les rendre plus évolutives et réactives. Par exemple, en appelant [SentimentBatchAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) au lieu de [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle du [SDK .NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte

Obtenez une clé pour authentifier vos applications auprès d’Azure Cognitive Services. Ces services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Analyse de texte en utilisant le [portail Azure](../../cognitive-services-apis-create-account.md) ou [Azure CLI](../../cognitive-services-apis-create-account-cli.md) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services/#decision) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle sera disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’évaluation, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Créer une application C#

Créez une application .NET Core dans votre éditeur ou IDE favori. 

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `text-analytics quickstart`. Cette commande crée un projet C# simple nommé « Hello World » avec un seul fichier source : *program.cs*. 

```console
dotnet new console -n text-analytics-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```console
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

À partir du répertoire de projet, ouvrez le fichier *program.cs* dans votre éditeur ou votre IDE favori. Ajoutez les directives `using` suivantes :

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

Dans la classe `Program` de l’application, créez des variables pour le point de terminaison et la clé d’abonnement Azure de votre ressource. Dans un constructeur statique, obtenez ces valeurs à partir des variables d’environnement `TEXT_ANALYTICS_SUBSCRIPTION_KEY` et `TEXT_ANALYTICS_ENDPOINT`. Si vous avez créé ces variables d’environnement après avoir commencé à modifier l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou l’interpréteur de commandes que vous utilisez pour accéder aux variables.

```csharp
private const string key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
```

Dans la méthode `Main` de l’application, créez des informations d’identification pour accéder au point de terminaison Analyse de texte.  Vous définirez plus tard les méthodes appelées par la méthode `Main`.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```csharp
static void Main(string[] args)
{
    var credentials = new ApiKeyServiceClientCredentials(subscriptionKey);
    TextAnalyticsClient client = new TextAnalyticsClient(credentials)
    {
        Endpoint = endpoint
    };

    Console.OutputEncoding = System.Text.Encoding.UTF8;
    SentimentAnalysisExample(client);
    // languageDetectionExample(client);
    // entityRecognitionExample(client);
    // KeyPhraseExtractionExample(client);
    
    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l’application, installez la bibliothèque de client Analyse de texte pour .NET avec la commande suivante :

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.

## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) qui s’authentifie auprès d’Azure à l’aide de votre clé et fournit des fonctions permettant d’accepter du texte sous forme de chaînes uniques ou de lots de chaînes. Vous pouvez envoyer du texte à l’API de façon synchrone ou de façon asynchrone. L’objet Response contient les informations d’analyse de chaque document que vous envoyez. 


## <a name="code-examples"></a>Exemples de code

* [Authentifier le client](#authenticate-the-client)
* [Analyse des sentiments](#sentiment-analysis)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entités](#entity-recognition)
* [Extraction de phrases clés](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Authentifier le client

Créez une classe `ApiKeyServiceClientCredentials` pour stocker les informations d’identification et les ajouter aux requêtes du client. Dans cette classe, créez une substitution pour `ProcessHttpRequestAsync()` qui ajoute votre clé à l’en-tête `Ocp-Apim-Subscription-Key`.

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

Dans la méthode `main()`, instanciez le client avec votre clé et votre point de terminaison.

```csharp
var credentials = new ApiKeyServiceClientCredentials(key);
TextAnalyticsClient client = new TextAnalyticsClient(credentials)
{
    Endpoint = endpoint
};
```

## <a name="sentiment-analysis"></a>analyse de sentiments

Créez une nouvelle fonction appelée `SentimentAnalysisExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet). L’objet [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) retourné contiendra le sentiment `Score` si l’opération réussit, et `errorMessage` si elle échoue. 

Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client){
    var result = client.Sentiment("I had the best day of my life.", "en");
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
}
```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Détection de la langue

Créez une nouvelle fonction appelée `languageDetectionExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). L’objet [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) retourné contiendra la liste des langues détectées dans `DetectedLanguages` si l’opération réussit, et `errorMessage` si elle échoue.  Imprimez la première langue retournée.

> [!Tip]
> Dans certains cas, il peut être difficile de lever toute ambiguïté sur les langues en fonction de l’entrée. Vous pouvez utiliser le paramètre `countryHint` pour spécifier un code de pays à 2 lettres. L’API utilise « US » comme countryHint par défaut. Pour modifier ce comportement, vous pouvez réinitialiser ce paramètre en définissant cette valeur sur une chaîne vide `countryHint = ""`.

```csharp
static void languageDetectionExample(TextAnalyticsClient client){
    var result = client.DetectLanguage("This is a document written in English.");
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
}
```
<!--
[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>Output

```console
Language: English
```

## <a name="entity-recognition"></a>Reconnaissance d’entité

Créez une nouvelle fonction appelée `RecognizeEntitiesExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Effectuez une itération dans les résultats. L’objet [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) retourné contiendra la liste des entités détectées dans `Entities` si l’opération réussit, et `errorMessage` si elle échoue. Pour chaque entité détectée, imprimez son type, son sous-type, son nom Wikipédia (s’il existe), ainsi que les emplacements dans le texte d’origine.

```csharp
static void entityRecognitionExample(TextAnalyticsClient client){

    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities){
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches){
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
}
```
<!--
[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

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

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Créez une fonction appelée `KeyPhraseExtractionExample()` qui accepte le client que vous avez créé précédemment, puis appelez sa fonction [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Le résultat contiendra la liste des expressions clés détectées dans `KeyPhrases` si l’opération réussit et un `errorMessage` si ce n’est pas le cas. Imprimez les expressions clés détectées.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
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

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources liées au groupe de ressources.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconnaissance d’entités](../how-tos/text-analytics-how-to-entity-linking.md)
* [Détecter la langue](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconnaissance de la langue](../how-tos/text-analytics-how-to-language-detection.md)
