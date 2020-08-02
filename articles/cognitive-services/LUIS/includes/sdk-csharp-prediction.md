---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 4bf86c616420bb049e1d7a82ad0e942e6eb7b36f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369252"
---
Utilisez la bibliothèque cliente de prédiction Language Understanding (LUIS) pour .NET afin de :

* Obtenir une prédiction par emplacement
* Prédiction par version

[Documentation de référence](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Package du runtime de prédiction (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [Exemples C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Prérequis

* Compte de portail Language Understanding (LUIS) - [Créez-en un gratuitement](https://www.luis.ai)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Un ID d’application LUIS : utilisez l’ID d’application IoT publique `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La requête utilisateur utilisée dans le code du guide de démarrage rapide est propre à cette application.

## <a name="setting-up"></a>Configuration

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

1. Créez des variables pour la clé, le nom de la ressource, l’ID de l’application et l’emplacement de publication. Définissez l’ID d’application sur l’application IoT publique :

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
