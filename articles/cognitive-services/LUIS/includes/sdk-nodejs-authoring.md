---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 6e240a0c5d5d77489c92862238c2e5041bdeabe3
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171336"
---
Utilisez la bibliothèque de client de création LUIS pour Node.js afin de :

* Créer une application.
* Ajouter des intentions, des entités et des exemples d’énoncés.
* Ajouter des fonctionnalités telles qu’une liste d’expressions.
* Entraîner et publier une application.
* Supprimer l’application

[Documentation de référence](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [Package de création (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [Package du runtime (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Prérequis

* Ressource de création LUIS : [vous devez en créer une dans le portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).
* [Node.JS](https://nodejs.org)

## <a name="setting-up"></a>Configuration

### <a name="get-your-language-understanding-luis-starter-key"></a>Obtenir votre clé de création LUIS (Language Understanding)

Obtenez votre [clé de démarrage](../luis-how-to-azure-subscription.md#starter-key) en créant une ressource de création LUIS. Notez votre clé et le point de terminaison de celle-ci pour l’étape suivante.

### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

À l’aide de votre clé et de sa région, créez deux variables d’environnement pour l’authentification :

* `LUIS_AUTHORING_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `LUIS_AUTHORING_ENDPOINT` : point de terminaison associé à votre clé.

Utilisez les instructions pour votre système d’exploitation.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Après avoir ajouté la variable d’environnement, redémarrez la fenêtre de console.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macos"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Installer la bibliothèque NPM pour la création LUIS

Dans le répertoire de l’application, installez les dépendances avec la commande suivante :

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Modèle objet

Le client de création Language Understanding (LUIS) est un objet [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) qui s’authentifie auprès d’Azure et contient votre clé de création.

Une fois le client créé, utilisez-le pour accéder aux fonctionnalités, notamment :

* Applications : [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exemples d’énoncés : [ajouter par lot](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [supprimer par ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Fonctionnalités : gérer les [listes d’expressions](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modèle : gérer les [intentions](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) et les entités
* Modèle : gérer les [modèles](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Entraînement : [entraînez](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) l’application et interrogez l’[état de l’entraînement](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versions](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) : gérer avec clonage, exportation et suppression


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client de création Language Understanding (LUIS) pour Node.js :

* [Créer une application](#create-a-luis-app)
* [Ajouter des entités](#create-entities-for-the-app)
* [Ajouter des intentions](#create-intent-for-the-app)
* [Ajouter des exemples d’énoncés](#add-example-utterance-to-intent)
* [Entraîner l’application](#train-the-app)
* [Publier l’application](#publish-a-language-understanding-app)
* [Supprimer l’application](#delete-a-language-understanding-app)
* [Lister les applications](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans votre éditeur ou IDE favori, créez un fichier texte que vous nommerez `luis_authoring_quickstart.js`. Ensuite, ajoutez les dépendances suivantes.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Dependencies)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Authentifier le client

Créez un objet [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest).

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Créer une application LUIS

1. Créez une application LUIS pour stocker le modèle de traitement en langage naturel contenant les intentions, les entités et les exemples d’énoncés.

1. Créez une méthode [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) pour l’objet [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) afin de créer l’application. Le nom et la culture de la langue sont des propriétés obligatoires.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Créer une intention pour l’application
L’objet principal dans un modèle d’application LUIS est l’intention. L’intention s’aligne sur un regroupement d’_intentions_ d’énoncés utilisateur. Un utilisateur peut poser une question ou émettre un énoncé en souhaitant obtenir une réponse _prévue_ particulière d’un bot (ou d’une autre application cliente). Réserver un billet d’avion, demander quelle est la météo dans une ville de destination et demander des informations de contact pour un service client sont des exemples d’intentions.

Utilisez la méthode [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) avec le nom de l’intention unique, puis transmettez l’ID de l’application, l’ID de version et le nom de la nouvelle intention.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Créer des entités pour l’application

Bien que les entités ne soient pas obligatoires, elles sont présentes dans la plupart des applications. L’entité extrait des informations à partir de l’énoncé utilisateur, qui sont nécessaires pour répondre à l’intention de l’utilisateur. Il existe plusieurs types d’entités [prédéfinies](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) et personnalisées, chacune avec leurs propres modèles DTO (Data Transformation Object).  Les entités prédéfinies courantes à ajouter à votre application incluent [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) et [ordinal](../luis-reference-prebuilt-ordinal.md).

Cette méthode **add_entities** crée une entité simple `Location` avec deux rôles, une entité simple `Class`, une entité composite `Flight`, et ajoute plusieurs entités prédéfinies.

Il est important de savoir que les entités ne sont pas marquées avec une intention. Elles peuvent s’appliquer à de nombreuses intentions. Seuls les exemples d’énoncés utilisateur sont marqués pour une intention unique spécifique.

Les méthodes de création pour les entités font partie de la classe [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest). Chaque type d’entité possède son propre modèle DTO (Data Transformation Object).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Ajouter un exemple d’énoncé à une intention

Pour déterminer l’intention d’un énoncé et extraire des entités, l’application a besoin d’exemples d’énoncés. Les exemples doivent cibler une intention spécifique et unique, et doivent marquer toutes les entités personnalisées. Les entités prédéfinies n’ont pas besoin d’être marquées.

Ajoutez des exemples d’énoncés en créant une liste d’objets [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest), un objet pour chaque exemple d’énoncé. Chaque exemple doit marquer toutes les entités avec un dictionnaire de paires nom/valeur de nom d’entité et de valeur d’entité. La valeur de l’entité doit être exactement telle qu’elle apparaît dans le texte de l’exemple d’énoncé.

Appelez [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) avec l’ID de l’application, l’ID de version et la liste des exemples. L’appel répond avec une liste de résultats. Vous devez vérifier le résultat de chaque exemple pour vous assurer qu’il a été correctement ajouté au modèle.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

Une fois le modèle créé, l’application LUIS doit être entraînée pour cette version du modèle. Un modèle entraîné peut être utilisé dans un [conteneur](../luis-container-howto.md) ou [publié](../luis-how-to-publish-app.md) dans les emplacements intermédiaires ou produits.

La méthode [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) a besoin de l’ID d’application et de l’ID de version.

Un modèle très petit, comme l’illustre ce guide de démarrage rapide, sera entraîné très rapidement. Pour les applications de niveau production, l’entraînement de l’application doit inclure un appel d’interrogation à la méthode [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) pour déterminer si l’entraînement a réussi. La réponse est une liste d’objets [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) avec un état distinct pour chaque objet. Tous les objets doivent réussir pour que l’entraînement soit considéré comme terminé.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

L’entraînement de tous les modèles prend du temps. Utilisez operationResult pour vérifier l’état de l’entraînement.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publier une application Language Understanding

Publiez l’application LUIS à l’aide de la méthode [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-). Cela permet de publier la version entraînée actuelle à l’emplacement spécifié au point de terminaison. Votre application cliente utilise ce point de terminaison afin d’envoyer des énoncés utilisateur pour la prédiction de l’intention et l’extraction d’entité.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Supprimer une application LUIS

Supprimez l’application LUIS avec la méthode [app.deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-). Cette méthode supprime l’application actuelle.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Lister des applications LUIS

Obtenir la liste des applications associées à la clé LUIS

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node luis_authoring_quickstart.js` de votre fichier de démarrage rapide.

```console
node luis_authoring_quickstart.js
```

La sortie de la ligne de commande de l’application est la suivante :

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
