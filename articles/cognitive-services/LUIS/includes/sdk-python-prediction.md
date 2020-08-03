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
ms.openlocfilehash: db866da43310f5407ce4daae1cade2c7512b91ea
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369251"
---
Utilisez la bibliothèque de client de prédiction LUIS (Language Understanding) pour Python pour :

* Obtenir une prédiction par emplacement
* Obtenir une prédiction par version

[Documentation de référence](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Package du runtime de prédiction (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [ Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py)

## <a name="prerequisites"></a>Prérequis

* Compte de portail Language Understanding (LUIS) - [Créez-en un gratuitement](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* Un ID d’application LUIS : utilisez l’ID d’application IoT publique `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La requête utilisateur utilisée dans le code du guide de démarrage rapide est propre à cette application.

## <a name="setting-up"></a>Configuration

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenir votre clé de runtime LUIS (Language Understanding)

Obtenez votre [clé de runtime](../luis-how-to-azure-subscription.md) en créant une ressource de runtime LUIS. Notez votre clé et le point de terminaison de celle-ci pour l’étape suivante.

### <a name="create-a-new-python-file"></a>Créer un fichier Python

Créez un fichier Python dans votre éditeur ou IDE favori, en le nommant `prediction_quickstart.py`.

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Dans le répertoire d’application, installez la bibliothèque de client du runtime de prédiction LUIS (Language Understanding) pour Python, à l’aide de la commande suivante :

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modèle objet

Le client du runtime de prédiction Language Understanding (LUIS) est un objet [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) qui s’authentifie auprès d’Azure et contient votre clé de ressource.

Une fois le client créé, utilisez-le pour accéder aux fonctionnalités, notamment :

* Prédiction par [emplacement de préproduction ou de production](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Prédiction par [version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les opérations suivantes à l’aide de la bibliothèque de client du runtime de prédiction LUIS (Language Understanding) pour Python :

* [Prédiction par emplacement](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Ajouter les dépendances

À partir du répertoire de projet, ouvrez le fichier `prediction_quickstart.py` dans votre éditeur ou IDE favori. Ajoutez les dépendances suivantes :

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Authentifier le client

1. Créez des variables pour vos propres informations LUIS requises : votre clé de prédiction et votre point de terminaison.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=AuthorizationVariables)]

1. Créez une variable pour l’ID d’application défini sur l’application IoT publique **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Créez une variable pour définir l’emplacement publié `production`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=OtherVariables)]


1. Créez un objet d’informations d’identification avec votre clé, puis utilisez-le avec votre point de terminaison pour créer un objet [LUISRuntimeClientConfiguration](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python).

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Obtenir une prédiction du runtime

Ajoutez la méthode suivante pour créer la requête au runtime de prédiction.

L’énoncé utilisateur fait partie de l’objet [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python).

La méthode **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** nécessite plusieurs paramètres tels que l’ID de l’application, le nom de l’emplacement et l’objet de requête de prédiction pour traiter la requête. Les autres options que sont verbose (mode détaillé), showAllIntents (afficher toutes les intentions) et log (journal) sont facultatives. La requête retourne un objet [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python).

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Code principal pour la prédiction

Utilisez la méthode Main ci-dessous pour lier les variables et les méthodes ensemble de façon à obtenir la prédiction.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python prediction_quickstart.py` à partir du répertoire de votre application.

```console
python prediction_quickstart.py
```

La console du guide de démarrage rapide affiche la sortie :

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois vos prédictions effectuées, nettoyez le travail accompli dans ce guide de démarrage rapide en supprimant le fichier et ses sous-répertoires.
