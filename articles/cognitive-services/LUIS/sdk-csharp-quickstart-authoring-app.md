---
title: 'Démarrage rapide : Bibliothèque de client de création Language Understanding (LUIS) pour .NET'
titleSuffix: Azure Cognitive Services
description: Commencez à utiliser la bibliothèque de client LUIS pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 49a28fb779b7a48b598059e9494cb28e9ec57a6e
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405897"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>Démarrage rapide : Bibliothèque de client de création Language Understanding (LUIS) pour .NET

Découvrez comment bien démarrer avec la bibliothèque de client de création Language Understanding (LUIS) pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  Language Understanding (LUIS) vous permet d’appliquer une intelligence Machine Learning personnalisée au texte en langage naturel des conversations d’un utilisateur afin d’en prédire le sens général et d’en extraire des informations détaillées et pertinentes. 

Utilisez la bibliothèque de client de création Language Understanding (LUIS) pour .NET afin de :

* Créer une application
* Ajouter des intentions, des entités et des exemples d’énoncés.
* Ajouter des fonctionnalités telles qu’une liste d’expressions.
* Entraîner et publier une application.

[Documentation de référence](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [Package de création (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [Exemples C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Prérequis

* Compte de portail Language Understanding (LUIS) - [Créez-en un gratuitement](https://www.luis.ai)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)


## <a name="setting-up"></a>Configuration

### <a name="get-your-language-understanding-luis-starter-key"></a>Obtenir votre clé de création LUIS (Language Understanding)

Obtenez votre [clé de démarrage](luis-how-to-azure-subscription.md#starter-key) en créant une ressource de création LUIS. Notez votre clé et la région de celle-ci pour l’étape suivante.

### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

À l’aide de votre clé et de sa région, créez deux variables d’environnement pour l’authentification :

* `COGNITIVESERVICE_AUTHORING_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `COGNITIVESERVICE_REGION` : région associée à votre clé. Par exemple, `westus`.

Utilisez les instructions pour votre système d’exploitation.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Après avoir ajouté la variable d’environnement, redémarrez la fenêtre de console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key> 
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
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

Dans le répertoire de l’application, installez la bibliothèque de client de création Language Understanding (LUIS) pour .NET avec la commande suivante :

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.


## <a name="object-model"></a>Modèle objet

Le client de création Language Understanding (LUIS) est un objet [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) qui s’authentifie auprès d’Azure et contient votre clé de création.

Une fois le client créé, utilisez-le pour accéder aux fonctionnalités, notamment :

* Applications : [créer](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [supprimer](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publier](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Exemples d’énoncés : [ajouter par lot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [supprimer par ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* Fonctionnalités : gérer les [listes d’expressions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) 
* Modèle : gérer les [intentions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) et les entités
* Modèle : gérer les [modèles](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Entraînement : [entraînez](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) l’application et interrogez l’[état de l’entraînement](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) : gérer avec clonage, exportation et suppression


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client de création Language Understanding (LUIS) pour .NET :

* [Créer une application](#create-a-luis-app)
* [Ajouter des entités](#create-entities-for-the-app)
* [Ajouter des intentions](#create-intent-for-the-app)
* [Ajouter des exemples d’énoncés](#add-example-utterance-to-intent)
* [Entraîner l’application](#train-the-app)
* [Publier l’application](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Ajouter les dépendances

À partir du répertoire de projet, ouvrez le fichier *Program.cs* dans votre éditeur ou votre IDE favori. Remplacez le code `using` existant par les directives `using` suivantes :

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Authentifier le client

1. Créez une variable pour gérer votre clé de création extraite à partir d’une variable d’environnement nommée `COGNITIVESERVICES_AUTHORING_KEY`. Si vous avez créé la variable d’environnement après le lancement de l’application, l’éditeur, l’IDE ou le shell l’exécutant doit être fermé et rechargé pour accéder à la variable. Les méthodes seront créées ultérieurement.

1. Créez des variables pour stocker votre région et votre point de terminaison de création. La région de votre clé de création dépend de l’emplacement de création. Les [trois régions de création](luis-reference-regions.md) sont les suivantes :

    * Australie : `australiaeast`
    * Europe : `westeurope`
    * États-Unis et autres régions : `westus` (par défaut)
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Créez un objet [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Créer une application LUIS

1. Créez une application LUIS pour stocker le modèle de traitement en langage naturel contenant les intentions, les entités et les exemples d’énoncés. 

1. Créez un [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Le nom et la culture de la langue sont des propriétés obligatoires. 

1. Appelez la méthode [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet). La réponse est l’ID d’application. 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Créer une intention pour l’application
L’objet principal dans un modèle d’application LUIS est l’intention. L’intention s’aligne sur un regroupement d’_intentions_ d’énoncés utilisateur. Un utilisateur peut poser une question ou émettre un énoncé en souhaitant obtenir une réponse _prévue_ particulière d’un bot (ou d’une autre application cliente). Réserver un billet d’avion, demander quelle est la météo dans une ville de destination et demander des informations de contact pour un service client sont des exemples d’intentions.   

Créez un [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) avec le nom de l’intention unique, puis transmettez l’ID d’application, l’ID de version et le ModelCreateObject à la méthode [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet). La réponse est l’ID d’intention.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Créer des entités pour l’application

Bien que les entités ne soient pas obligatoires, elles sont présentes dans la plupart des applications. L’entité extrait des informations à partir de l’énoncé utilisateur, qui sont nécessaires pour répondre à l’intention de l’utilisateur. Il existe plusieurs types d’entités [prédéfinies](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) et personnalisées, chacune avec leurs propres modèles DTO (Data Transformation Object).  Les entités prédéfinies courantes à ajouter à votre application incluent [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md) et [ordinal](luis-reference-prebuilt-ordinal.md). 

Cette méthode **AddEntities** crée une entité simple `Location` avec deux rôles, une entité simple `Class`, une entité composite `Flight`, et ajoute plusieurs entités prédéfinies.

Il est important de savoir que les entités ne sont pas marquées avec une intention. Elles peuvent s’appliquer à de nombreuses intentions. Seuls les exemples d’énoncés utilisateur sont marqués pour une intention unique spécifique.

Les méthodes de création pour les entités font partie de la classe [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet). Chaque type d’entité a son propre modèle DTO, qui contient généralement le mot `model` dans l’espace de noms [Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet). 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Ajouter un exemple d’énoncé à une intention

Pour déterminer l’intention d’un énoncé et extraire des entités, l’application a besoin d’exemples d’énoncés. Les exemples doivent cibler une intention spécifique et unique, et doivent marquer toutes les entités personnalisées. Les entités prédéfinies n’ont pas besoin d’être marquées. 

Ajoutez des exemples d’énoncés en créant une liste d’objets [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet), un objet pour chaque exemple d’énoncé. Chaque exemple doit marquer toutes les entités avec un dictionnaire de paires nom/valeur de nom d’entité et de valeur d’entité. La valeur de l’entité doit être exactement telle qu’elle apparaît dans le texte de l’exemple d’énoncé. 

Appelez [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) avec l’ID d’application, l’ID de version et la liste des exemples. L’appel répond avec une liste de résultats. Vous devez vérifier le résultat de chaque exemple pour vous assurer qu’il a été correctement ajouté au modèle. 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
Les méthodes **CreateUtterance** et **CreateLabel** sont des méthodes utilitaires pour vous aider à créer des objets.

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

Une fois le modèle créé, l’application LUIS doit être entraînée pour cette version du modèle. Un modèle entraîné peut être utilisé dans un [conteneur](luis-container-howto.md) ou [publié](luis-how-to-publish-app.md) dans les emplacements intermédiaires ou produits. 

La méthode [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) a besoin de l’ID d’application et de l’ID de version. 

Un modèle très petit, comme l’illustre ce guide de démarrage rapide, sera entraîné très rapidement. Pour les applications de niveau production, l’entraînement de l’application doit inclure un appel d’interrogation à la méthode [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) pour déterminer si l’entraînement a réussi. La réponse est une liste d’objets [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) avec un état distinct pour chaque objet. Tous les objets doivent réussir pour que l’entraînement soit considéré comme terminé.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publier une application Language Understanding

Publiez l’application LUIS à l’aide de la méthode [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet). Cela permet de publier la version entraînée actuelle à l’emplacement spécifié au point de terminaison. Votre application cliente utilise ce point de terminaison afin d’envoyer des énoncés utilisateur pour la prédiction de l’intention et l’extraction d’entité.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `dotnet run` à partir du répertoire de votre application.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez effectuer un nettoyage, vous pouvez supprimer l’application LUIS. La suppression de l’application s’effectue à l’aide de la méthode [Apps.DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet). Vous pouvez également supprimer l’application à partir du [portail LUIS](https://www.luis.ai). 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Utiliser le SDK .NET pour interroger le point de terminaison de prédiction](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [Qu’est-ce que l’API Language Understanding (LUIS) ?](what-is-luis.md)
* [Nouveautés](whats-new.md)
* [Intentions](luis-concept-intent.md), [entités](luis-concept-entity-types.md), [exemples d’énoncés](luis-concept-utterance.md) et [entités prédéfinies](luis-reference-prebuilt-entities.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs).
