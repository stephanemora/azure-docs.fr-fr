---
title: Modifier, effectuer l’apprentissage d’application, Node.js
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide de Node.js, vous allez ajouter des exemples d’énoncés à une application de domotique et effectuer l’apprentissage de l’application.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/17/2019
ms.author: diberry
ms.openlocfilehash: 6ee3ecc37cfb21630b671e70260b44f1532de2b6
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594131"
---
# <a name="quickstart-change-model-using-nodejs"></a>Démarrage rapide : Changer de modèle à l’aide de Node.js

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Dernière version de [**Node.js**](https://nodejs.org/en/download/) avec NPM.
* Dépendances NPM pour cet article : [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Créer un code de démarrage rapide 

Ajoutez les dépendances NPM au fichier nommé `add-utterances.js`.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Ajoutez les constantes LUIS au fichier. Copiez le code suivant et remplacez les valeurs par votre clé de création, ID d’application et ID de version.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Ajoutez le nom et l’emplacement du fichier de chargement contenant vos énoncés. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Ajoutez la méthode et l’objet pour la fonction `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Ajoutez la méthode et l’objet pour la fonction `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Ajoutez la fonction `sendUtteranceToApi` pour envoyer et recevoir des appels HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Ajoutez le code **principal** qui choisit l’action à exécuter.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Installer des dépendances

Créez le fichier `package.json` avec le texte suivant :

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

Dans la ligne de commande, depuis le répertoire qui contient le fichier package.json, installez les dépendances NPM : `npm install`.

## <a name="run-code"></a>Exécuter le code

Exécutez l’application à partir d’une ligne de commande avec Node.js.

Appeler `npm start` permet d’ajouter les énoncés, d’effectuer l’apprentissage et d’obtenir l’état d’apprentissage.

```console
> npm start 
```

Cette ligne de commande affiche les résultats de l’appel de l’API d’ajout d’énoncés. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois le démarrage rapide terminé, supprimez tous les fichiers créés pour ce démarrage rapide. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Créer une application LUIS par programme](luis-tutorial-node-import-utterances-csv.md)
