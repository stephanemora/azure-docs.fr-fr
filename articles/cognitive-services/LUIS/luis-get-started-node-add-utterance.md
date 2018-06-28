---
title: 'Didacticiel : Apprendre à ajouter des énoncés à une application LUIS à l’aide de Node.js | Microsoft Docs'
description: Dans ce didacticiel, vous allez apprendre à appeler une application LUIS à l’aide de Node.js.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: a1e028395ce4b1679a367ce110c2ba120128c501
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264224"
---
# <a name="tutorial-add-utterances-app-using-nodejs"></a>Didacticiel : Ajouter des énoncés à une application à l’aide de Node.js 
Dans ce didacticiel, vous allez écrire un programme pour ajouter un énoncé à une intention à l’aide des API de création dans Node.js.

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer un projet de console Visual Studio 
> * Ajouter la méthode pour appeler l’API LUIS afin d’ajouter l’énoncé et d’effectuer l’apprentissage de l’application
> * Ajouter le fichier JSON contenant des exemples d’énoncés pour l’intention BookFlight
> * Exécuter la console et observer l’état d’apprentissage des énoncés

Pour plus d’informations, consultez la documentation technique pour les API [d’ajout d’exemple d’énoncé à une intention](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [d’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) et [d’état d’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="prerequisites"></a>Prérequis

* Dernière version de [**Node.js**](https://nodejs.org/en/download/) avec NPM.
* Dépendances NPM pour cet article : [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* **[Recommandé]**  [Visual Studio Code](https://code.visualstudio.com/) pour IntelliSense et le débogage.
* Votre **[clé de création](luis-concept-keys.md#authoring-key)** LUIS. Vous pouvez trouver cette clé dans les paramètres de compte du site web [LUIS](luis-reference-regions.md).
* Votre [**ID d’application**](./luis-get-started-create-app.md) LUIS existant. L’ID d’application est indiqué dans le tableau de bord de l’application. L’application LUIS avec les intentions et les entités utilisées dans le fichier `utterances.json` doit exister avant d’exécuter le code dans `add-utterances.js`. Le code dans cet article ne crée pas les entités, ni les intentions. Il ajoute uniquement les énoncés pour les intentions et entités existantes. 
* **L’ID de version** dans l’application qui reçoit les énoncés. L’ID par défaut est « 0,1 »
* Créez un fichier nommé projet `add-utterances.js` dans VSCode.

> [!NOTE] 
> Le fichier `add-utterances.js` complet est disponible dans le [référentiel Github **LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/tree/master/examples/add-utterances/nodejs) (Exemples-LUIS).


## <a name="write-the-nodejs-code"></a>Écrire le code Node.js

Ajoutez les dépendances NPM au fichier.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=16-19 "NPM Dependencies")]

Ajoutez les constantes LUIS au fichier. Copiez le code suivant et remplacez les valeurs par votre clé de création, ID d’application et ID de version.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=22-29 "LUIS key and IDs")]

Ajoutez le nom et l’emplacement du fichier de chargement contenant vos énoncés. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=31-33 "Add upload file")]

Ajoutez les variables qui contiennent les valeurs de ligne de commande.

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=35-49 "Add upload file")]


Ajoutez la fonction `sendUtteranceToApi` pour envoyer et recevoir des appels HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=135-151 "Send the HTTP request")]

Ajoutez l’objet JSON de configuration utilisé par la fonction `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=52-59 "Add configuration information for adding utterance")]

Ajoutez la fonction `addUtterance` pour gérer la requête d’API et la réponse utilisée par `SendUtteranceToApp`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=62-92 "Add configuration information for adding utterance")]

Ajoutez l’objet JSON de configuration utilisé par la fonction `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=94-101 "Add configuration information for training LUIS")]

Ajoutez la fonction `train` pour démarrer le processus d’apprentissage. 

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=103-133 "Train the application")]

Ajoutez le code qui choisit l’action à effectuer (ajouter un énoncé ou effectuer l’apprentissage) en fonction des variables de ligne de commande.

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=153-184 "Train the application")]

## <a name="specify-utterances-to-add"></a>Spécifier les énoncés à ajouter
Créez et modifiez le fichier `utterances.json` pour spécifier le **tableau d’énoncés** que vous souhaitez ajouter à l’application LUIS. L’intention et les entités **doivent** déjà se trouver dans l’application LUIS.

> [!NOTE]
> L’application LUIS avec les intentions et les entités utilisées dans le fichier `utterances.json` doit exister avant d’exécuter le code dans `add-utterances.js`. Le code dans cet article ne crée pas les entités, ni les intentions. Il ajoute uniquement les énoncés pour les intentions et entités existantes.

Le champ `text` contient le texte de l’énoncé. Le champ `intentName` doit correspondre au nom d’une intention dans l’application LUIS. Le champ `entityLabels` est obligatoire. Si vous ne souhaitez pas ajouter de libellé à toutes les entités, fournissez une liste vide comme indiqué dans l’exemple suivant.

Si la liste entityLabels n’est pas vide, les éléments `startCharIndex` et `endCharIndex` doivent marquer l’entité concernée dans le champ `entityName`. Les deux index sont des numérotations à partir de zéro, ce qui signifie que le 6 dans l’exemple correspond au « S » de Seattle et non à l’espace avant le S en majuscules.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Ajouter un énoncé à partir de la ligne de commande

Exécutez l’application à partir d’une ligne de commande avec Node.js.

Appeler add-utterance sans argument permet d’ajouter un énoncé à l’application, sans apprentissage.
````
> node add-utterances.js
````

Cet exemple crée un fichier avec le fichier `results.json` qui contient les résultats de l’appel de l’API d’ajout d’énoncés. Le champ `response` est dans ce format pour les énoncés qui ont été ajoutés. La valeur pour `hasError` est définie sur « false », ce qui signifie que l’énoncé a été ajouté.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Ajouter un énoncé et effectuer l’apprentissage à partir de la ligne de commande
Appelez add-utterance avec l’argument `-train` pour envoyer une requête d’apprentissage, puis demander l’état d’apprentissage. L’état est mis en file d’attente immédiatement après le début de l’apprentissage. Les détails de l’état sont consignés dans un fichier.

````
> node add-utterances.js -train
````

> [!NOTE]
> Les énoncés en double ne sont pas ajoutés une nouvelle fois, mais ne génèrent pas d’erreur. L’élément `response` contient l’ID de l’énoncé d’origine.

Lorsque vous appelez l’exemple avec l’argument `-train`, il crée un fichier `training-results.json` spécifiant la requête d’apprentissage de l’application LUIS qui a été mise en file d’attente. 

Le code suivant illustre le résultat d’une requête d’apprentissage ayant abouti :
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

Une fois la requête d’apprentissage mise en file d’attente, un certain temps peut être nécessaire pour que l’apprentissage ait lieu.

## <a name="get-training-status-from-the-command-line"></a>Obtenir l’état d’apprentissage à partir de la ligne de commande
Appelez l’exemple avec l’argument `-status` pour vérifier l’état d’apprentissage et consigner ses détails dans un fichier.

````
> node add-utterances.js -status
````

## <a name="clean-up-resources"></a>Supprimer des ressources
Une fois que vous avez terminé ce didacticiel, supprimez l’application console et Visual Studio si vous n’en avez plus besoin. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Créer une application LUIS par programme](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
