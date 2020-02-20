---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 05e668ff5b0ec19c5e380cf6bfee4b6e46900b2f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77371932"
---
Utilisez la bibliothèque de client du runtime LUIS (Language Understanding) pour Node.js afin de disposer des prédictions suivantes :

* Prédiction par emplacement
* Prédiction par version

[Documentation de référence](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Package du runtime (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Prérequis

* Ressource de runtime Language Understanding : [vous devez en créer une dans le portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).
* [Node.JS](https://nodejs.org)

## <a name="setting-up"></a>Configuration

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenir votre clé de runtime LUIS (Language Understanding)

Obtenez votre [clé de runtime](../luis-how-to-azure-subscription.md) en créant une ressource de runtime LUIS. Notez votre clé et le point de terminaison de celle-ci pour l’étape suivante.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Créer un fichier JavaScript (Node.js)

Créez un fichier JavaScript dans votre éditeur ou IDE favori, en le nommant `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Installer la bibliothèque NPM pour le runtime LUIS

Dans le répertoire de l’application, installez les dépendances avec la commande suivante :

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Modèle objet

Le client de création Language Understanding (LUIS) est un objet [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) qui s’authentifie auprès d’Azure et contient votre clé de création.

Une fois le client créé, utilisez-le pour accéder aux fonctionnalités, notamment :

* [Prédiction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) par emplacement de `staging` ou de `production`
* [Prédiction par version](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les opérations suivantes avec la bibliothèque de client du runtime de prédiction LUIS (Language Understanding) :

* [Prédiction par emplacement](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Ajouter les dépendances

À partir du répertoire de projet, ouvrez le fichier `luis_prediction.js` dans votre éditeur ou IDE favori. Ajoutez les dépendances suivantes :

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Authentifier le client

1. Créez des variables pour vos propres informations LUIS nécessaires :

    Ajoutez des variables pour gérer votre clé de prédiction tirée d’une variable d’environnement nommée `LUIS_RUNTIME_KEY`. Si vous avez créé la variable d’environnement après le lancement de l’application, l’éditeur, l’IDE ou le shell l’exécutant doit être fermé et rechargé pour accéder à la variable. Les méthodes seront créées ultérieurement.

    Créez une variable pour conserver votre nom de ressource `LUIS_RUNTIME_ENDPOINT`.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Créez une variable pour l’ID d’application sous forme de variable d’environnement nommée `LUIS_APP_ID`. Définissez la variable d’environnement en fonction de l’application IoT publique, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Créez une variable pour définir l’emplacement publié `production`.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Créez un objet msRest.ApiKeyCredentials avec votre clé, puis utilisez-le avec votre point de terminaison pour créer un objet [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest).

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Obtenir une prédiction du runtime

Ajoutez la méthode suivante pour créer la requête au runtime de prédiction.

L’énoncé utilisateur fait partie de l’objet [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest).

La méthode **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** nécessite plusieurs paramètres tels que l’ID de l’application, le nom de l’emplacement et l’objet de requête de prédiction pour traiter la requête. Les autres options que sont verbose (mode détaillé), showAllIntents (afficher toutes les intentions) et log (journal) sont facultatives.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Code principal pour la prédiction

Utilisez la méthode Main ci-dessous pour lier les variables et les méthodes ensemble de façon à obtenir la prédiction.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node luis_prediction.js` à partir du répertoire de votre application.

```console
node luis_prediction.js
```

Le résultat de la prédiction retourne un objet JSON :

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois vos prédictions effectuées, nettoyez le travail accompli dans ce guide de démarrage rapide en supprimant le fichier et ses sous-répertoires.
