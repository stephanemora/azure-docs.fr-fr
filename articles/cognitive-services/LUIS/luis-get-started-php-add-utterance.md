---
title: 'Didacticiel : Apprendre à ajouter des énoncés à une application LUIS à l’aide de PHP | Microsoft Docs'
description: Dans ce didacticiel, vous allez apprendre à appeler une application LUIS à l’aide de PHP.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 59150b7ed6782c28f243041be2ed6aa17e69cc01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263778"
---
# <a name="tutorial-add-utterances-to-app-using-php"></a>Didacticiel : Ajouter des énoncés à une application à l’aide de PHP 
Dans ce didacticiel, vous allez écrire un programme pour ajouter un énoncé à une intention à l’aide des API de création dans PHP.

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer un projet de console Visual Studio 
> * Ajouter la méthode pour appeler l’API LUIS afin d’ajouter l’énoncé et d’effectuer l’apprentissage de l’application
> * Ajouter le fichier JSON contenant des exemples d’énoncés pour l’intention BookFlight
> * Exécuter la console et observer l’état d’apprentissage des énoncés

Pour plus d’informations, consultez la documentation technique pour les API [d’ajout d’exemple d’énoncé à une intention](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [d’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) et [d’état d’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="prerequisites"></a>Prérequis

* Dernière version de [**PHP**](http://php.net/).
* Vérifiez qu’openssl est disponible en tant que dépendance pour PHP.  
* Votre **[clé de création](luis-concept-keys.md#authoring-key)** LUIS. Vous pouvez trouver cette clé dans les paramètres de compte du site web [LUIS](luis-reference-regions.md).
* Votre [**ID d’application**](./luis-get-started-create-app.md) LUIS existant. L’ID d’application est indiqué dans le tableau de bord de l’application. L’application LUIS avec les intentions et les entités utilisées dans le fichier `utterances.json` doit exister avant d’exécuter le code dans `add-utterances.php`. Le code dans cet article ne crée pas les entités, ni les intentions. Il ajoute uniquement les énoncés pour les intentions et entités existantes. 
* **L’ID de version** dans l’application qui reçoit les énoncés. L’ID par défaut est « 0,1 »
* Créez un fichier nommé projet `add-utterances.php` dans VSCode.

> [!NOTE] 
> Le fichier `add-utterances.cs` complet et un exemple de fichier `utterances.json` sont disponibles dans le [référentiel Github **LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/php/) (Exemples-LUIS).


## <a name="write-the-php-code"></a>Écrire le code PHP

Ajoutez les dépendances au fichier.

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=10-29 "PHP and LUIS Dependencies")]

Ajoutez la requête GET utilisée pour l’état d’apprentissage.

   [!code-php[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=31-50 "SendGet")]

Ajoutez la requête POST utilisée pour créer des énoncés ou commencer l’apprentissage. 

   [!code-php[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=52-72 "SendPost")]

Ajoutez la fonction `AddUtterances`.

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=74-79 "AddUtterances method")]


Ajoutez la fonction `Train`. 

   [!code-php[Train](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=81-88 "Train")]

Ajoutez la fonction `Status`.

   [!code-php[Status](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=90-94 "Status")]

Pour gérer les arguments de ligne de commande, ajoutez le bloc de code principal.

   [!code-php[Main code](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=96-120 "Main code")]

## <a name="specify-utterances-to-add"></a>Spécifier les énoncés à ajouter
Créez et modifiez le fichier `utterances.json` pour spécifier le **tableau d’énoncés** que vous souhaitez ajouter à l’application LUIS. L’intention et les entités **doivent** déjà se trouver dans l’application LUIS.

> [!NOTE]
> L’application LUIS avec les intentions et les entités utilisées dans le fichier `utterances.json` doit exister avant d’exécuter le code dans `add-utterances.php`. Le code dans cet article ne crée pas les entités, ni les intentions. Il ajoute uniquement les énoncés pour les intentions et entités existantes.

Le champ `text` contient le texte de l’énoncé. Le champ `intentName` doit correspondre au nom d’une intention dans l’application LUIS. Le champ `entityLabels` est obligatoire. Si vous ne souhaitez pas ajouter de libellé à toutes les entités, fournissez une liste vide comme indiqué dans l’exemple suivant :

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

Exécutez l’application à partir d’une ligne de commande avec PHP.

Appeler `add-utterances.php` avec uniquement le fichier utterance.json en tant qu’argument permet d’ajouter l’application LUIS. Toutefois, cette opération ne permet pas d’effectuer l’apprentissage sur les nouveaux énoncés.
````
> php add-utterances.php ./utterances.json
````

Le texte JSON suivant est renvoyé de l’appel d’API d’ajout d’énoncés. Le champ `response` est dans ce format pour les énoncés qui ont été ajoutés. La valeur pour `hasError` est définie sur « false », ce qui signifie que l’énoncé a été ajouté.  

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
Appelez `add-utterance.php` avec l’argument `-train` pour envoyer une requête d’apprentissage. 

````
> php add-utterances.php ./utterances.json -train
````

> [!NOTE]
> Les énoncés en double ne sont pas ajoutés une nouvelle fois, mais ne génèrent pas d’erreur. L’élément `response` contient l’ID de l’énoncé d’origine.

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
Appelez l’application avec l’argument `-status` pour vérifier l’état d’apprentissage et en afficher les détails.

````
> php add-utterances.php -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Une fois que vous avez terminé ce didacticiel, supprimez l’application console et Visual Studio si vous n’en avez plus besoin. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Créer une application LUIS par programme](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website