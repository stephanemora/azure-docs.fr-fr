---
title: 'Démarrage rapide : Point de terminaison de prédiction d’interrogation - SDK C# - LUIS'
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment utiliser le SDK C# pour envoyer un énoncé utilisateur à l’application LUIS Azure Cognitive Services et recevoir une prédiction.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f4612f7b3f76cbbfc0deac98668770f92ff054bc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953423"
---
# <a name="quickstart-query-v2-prediction-endpoint-with-c-net-sdk"></a>Démarrage rapide : Envoyer une requête à un point de terminaison de prédiction V2 avec le SDK .NET C#

Utilisez le SDK .NET, disponible sur [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), pour envoyer un énoncé utilisateur à LUIS (Language Understanding) et recevoir une prédiction de l’intention de l’utilisateur. 

Ce guide de démarrage rapide envoie un énoncé utilisateur tel que `turn on the bedroom light` à une application Language Understanding, puis reçoit la prédiction et affiche l’intention avec le meilleur score `HomeAutomation.TurnOn` et l’entité `HomeAutomation.Room` trouvée au sein de l’énoncé. 

## <a name="prerequisites"></a>Prérequis

* [Visual Studio Community Edition 2017](https://visualstudio.microsoft.com/vs/community/)
* Langage de programmation C# (inclus avec VS Community 2017)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> La solution complète est disponible dans le dépôt GitHub [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime).

Vous cherchez plus de documentation ?

 * [Documentation de référence du SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Obtenir une clé Cognitive Services ou Language Understanding

Pour utiliser l’application publique de domotique, vous devez disposer d’une clé valide pour les prédictions de point de terminaison. Vous pouvez soit utiliser une clé Cognitive Services, créée ci-dessous avec l’interface Azure CLI et valide pour de nombreux services cognitifs, soit utiliser une clé `Language Understanding`. 

Utilisez la [commande Azure CLI suivante pour créer une clé Cognitive Services](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) :

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Créer un projet .NET Core

Créez un projet de console .NET Core dans Visual Studio Community 2017.

1. Ouvrez Visual Studio Community 2017.
1. Créez un projet. Dans la section **Visual C#** , choisissez **Application console (.NET Core)** .
1. Entrez le nom de projet `QueryPrediction`, conservez les valeurs par défaut restantes, puis sélectionnez **OK**.
    Cette opération crée un projet simple avec un fichier de code principal nommé **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Ajouter le SDK avec NuGet

1. Dans l’**Explorateur de solutions**, sélectionnez le projet dans l’arborescence nommée **QueryPrediction**, puis faites un clic droit. Dans le menu, sélectionnez **Gérer les packages NuGet...** .
1. Sélectionnez **Parcourir**, puis entrez `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Quand les informations du package s’affichent, sélectionnez **Installer** pour installer le package dans le projet. 
1. Ajoutez les instructions _using_ suivantes en haut de **Program.cs**. Ne supprimez pas l’instruction _using_ existante pour `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Créer une méthode pour la prédiction

Créez une méthode `GetPrediction` pour envoyer la requête au point de terminaison de prédiction de requête. La méthode crée et configure tous les objets nécessaires, puis retourne un `Task` avec les résultats de la prédiction [`LuisResult`](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.luisresult). 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Créer un objet d’informations d’identification

Ajoutez le code suivant à la méthode `GetPrediction` pour créer des informations d’identification de client avec votre clé Cognitive Services.

Remplacez `<REPLACE-WITH-YOUR-KEY>` par la région de votre clé Cognitive Services. La clé se trouve dans le [portail Azure](https://portal.azure.com), dans la page Clés de cette ressource.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Créer un client Language Understanding

Dans la méthode `GetPrediction`, après le code précédent, ajoutez le code suivant pour utiliser les nouvelles informations d’identification. Un objet client [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) est créé. 

Remplacez `<REPLACE-WITH-YOUR-KEY-REGION>` par la région de votre clé (par exemple, `westus`). La région de la clé se trouve dans le [portail Azure](https://portal.azure.com), dans la page Vue d’ensemble de cette ressource.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Définir les paramètres de la requête

Dans la méthode `GetPrediction`, après le code précédent, ajoutez le code suivant pour définir les paramètres de la requête.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Interroger le point de terminaison de prédiction

Dans la méthode `GetPrediction`, après le code précédent, ajoutez le code suivant pour définir les paramètres de la requête :

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Afficher les résultats de la prédiction

Changez la méthode **Main** pour appeler la nouvelle méthode `GetPrediction` et retourner les résultats de la prédiction :

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Exécuter le projet

Générez le projet dans Studio et exécutez le projet pour voir la sortie de la requête :

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) et la [documentation de référence .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Tutoriel : Générer une application LUIS pour déterminer les intentions d’un utilisateur](luis-quickstart-intents-only.md) 
