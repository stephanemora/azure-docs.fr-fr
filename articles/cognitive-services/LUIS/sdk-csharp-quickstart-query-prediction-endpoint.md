---
title: 'Démarrage rapide : Point de terminaison de prédiction d’interrogation - SDK C# - LUIS'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide vous montre comment utiliser le SDK C# pour envoyer un énoncé utilisateur à l’application LUIS Azure Cognitive Services et recevoir une prédiction.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 37e7224776efa63b39a671a3b3a79ea6c204a9dc
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611087"
---
# <a name="quickstart-query-v3-prediction-endpoint-with-c-net-sdk"></a>Démarrage rapide : Envoyer une requête à un point de terminaison de prédiction V3 avec le SDK .NET C#

Utilisez le SDK .NET, disponible sur [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), pour envoyer un énoncé utilisateur à LUIS (Language Understanding) et recevoir une prédiction de l’intention de l’utilisateur.

Utilisez la bibliothèque cliente de prédiction Language Understanding (LUIS) pour .NET afin de :

* Obtenir une prédiction par emplacement

[Documentation de référence](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Package du runtime de prédiction (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [Exemples C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Conditions préalables requises

* Compte de portail Language Understanding (LUIS) - [Créez-en un gratuitement](https://www.luis.ai)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

Vous cherchez plus de documentation ?

 * [Documentation de référence du SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Configuration

### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

En utilisant votre clé et le nom de votre ressource, créez deux variables d’environnement pour l’authentification :

* `LUIS_PREDICTION_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `LUIS_ENDPOINT_NAME` : nom de ressource associé à votre clé.

Utilisez les instructions pour votre système d’exploitation.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Après avoir ajouté la variable d’environnement, redémarrez la fenêtre de console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.
***

### <a name="create-a-new-c-application"></a>Créer une application C#

Créez une application .NET Core dans votre éditeur ou IDE favori.

1. Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `new` dotnet pour créer une application console avec le nom `language-understanding-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Déplacez vos répertoires vers le dossier d’application nouvellement créé.

1. Vous pouvez générer l’application avec :

    ```dotnetcli
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

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Dans le répertoire de l’application, installez la bibliothèque cliente du runtime de prédiction Language Understanding (LUIS) pour .NET avec la commande suivante :

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.

## <a name="object-model"></a>Modèle objet

Le client du runtime de prédiction Language Understanding (LUIS) est un objet [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) qui s’authentifie auprès d’Azure et contient votre clé de ressource.

Une fois le client créé, utilisez-le pour accéder aux fonctionnalités, notamment :

* Prédiction par [emplacement de préproduction ou de produit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Prédiction par [version](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque cliente du runtime de prédiction Language Understanding (LUIS) pour .NET :

* [Prédiction par emplacement](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Ajouter les dépendances

À partir du répertoire de projet, ouvrez le fichier *Program.cs* dans votre éditeur ou votre IDE favori. Remplacez le code `using` existant par les directives `using` suivantes :

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Authentifier le client

1. Créez des variables pour la clé, le nom et l’ID d’application :

    Créez une variable pour gérer votre clé de prédiction extraite à partir d’une variable d’environnement nommée `LUIS_PREDICTION_KEY`. Si vous avez créé la variable d’environnement après le lancement de l’application, l’éditeur, l’IDE ou le shell l’exécutant doit être fermé et rechargé pour accéder à la variable. Les méthodes seront créées ultérieurement.

    Créez une variable pour conserver votre nom de ressource `LUIS_ENDPOINT_NAME`.

    Créez une variable pour l’ID d’application sous forme de variable d’environnement nommée `LUIS_APP_ID`. Définissez la variable d’environnement sur l’application IoT publique :

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Créez un objet [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Obtenir une prédiction du runtime

Ajoutez la méthode suivante pour créer la requête au runtime de prédiction.

L’énoncé utilisateur fait partie de l’objet [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet).

La méthode **GetSlotPredictionAsync** a besoin de plusieurs paramètres, comme appID (ID d’application), slotName ( nom d’emplacement), l’objet predictionRequest (demande de prédiction), pour satisfaire la demande. Les autres options que sont verbose (mode détaillé), showAllIntents (afficher toutes les intentions) et log (journal) sont facultatives.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Code principal pour la prédiction

Utilisez la méthode Main ci-dessous pour lier les variables et les méthodes ensemble de façon à obtenir la prédiction.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `dotnet run` à partir du répertoire de votre application.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous en avez terminé avec les prédictions, nettoyez le travail effectué dans ce démarrage rapide en supprimant le fichier program.cs et ses sous-répertoires.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) et la [documentation de référence .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet).

> [!div class="nextstepaction"]
> [Tutoriel : Générer une application LUIS pour déterminer les intentions d’un utilisateur](luis-quickstart-intents-only.md)