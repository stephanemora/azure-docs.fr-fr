---
title: Didacticiel de création d’une application LUIS retournant des expressions clés - Azure | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à ajouter et à retourner une entité keyPhrase à votre application LUIS pour analyser les énoncés du thème principal.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: ef7a1c81f453a8d4ff9526a4844518782e152c4f
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159484"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>Tutoriel : 8. Ajouter une entité keyPhrase 
Dans ce tutoriel, vous allez utiliser une application montrant comment extraire les données principales des énoncés.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités keyPhrase 
> * Utiliser l’application LUIS pour le domaine des ressources humaines (RH) 
> * Ajouter une entité keyPhrase pour extraire le contenu d’un énoncé
> * Entraîner et publier l’application
> * Interroger un point de terminaison d’application pour voir la réponse JSON LUIS, y compris les expressions clé

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [entité simple](luis-quickstart-primary-and-secondary-data.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une application du site web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `keyphrase`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="keyphrase-entity-extraction"></a>Extraction de l’entité keyPhrase
Le thème principal est fourni par l’entité prédéfinie, **keyPhrase**. Cette entité retourne le thème principal de l’énoncé.

Les énoncés suivants présentent des exemples d’expressions clés :

|Énoncé|Valeurs d’entité keyPhrase|
|--|--|
|Y-a-t-il un nouveau régime d’assurance-maladie qui propose une franchise moins élevée l’année suivante ?|« franchise moins élevée »<br>« nouveau régime d’assurance-maladie »<br>« année »|
|La thérapie visuelle est-elle couverte par le régime d’assurance-maladie à franchise élevée ?|« régime d’assurance-maladie à franchise élevée »<br>« thérapie visuelle »|

Votre application cliente peut utiliser ces valeurs, ainsi que d’autres entités extraites, pour décider de l’étape suivante de la conversation.

## <a name="add-keyphrase-entity"></a>Ajouter une entité keyPhrase 
Ajoutez une entité prédéfinie keyPhrase pour extraire le thème des énoncés.

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

2. Dans le menu gauche, sélectionnez **Entités**.

    [ ![Capture d’écran du menu Entités mis en surbrillance dans le volet de navigation gauche de la section Build (Générer)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Sélectionnez **Manage prebuilt entities** (Gérer les entités prédéfinies).

    [ ![Capture d’écran de la boîte de dialogue contextuelle Entités](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. Dans la boîte de dialogue contextuelle, sélectionnez **keyPhrase**, puis **Terminé**. 

    [ ![Capture d’écran de la boîte de dialogue contextuelle Entités](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. Sélectionnez **Intentions** dans le menu de gauche, puis sélectionnez l’intention **Utilities.Confirm**. L’entité keyPhrase est étiquetée dans plusieurs énoncés. 

    [ ![Capture d’écran de l’intention Utilities.Confirm avec keyPhrases étiquetées dans les énoncés](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Publier l’application vers un point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]


## <a name="query-the-endpoint-with-an-utterance"></a>Interroger le point de terminaison avec un énoncé

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `does form hrf-123456 cover the new dental benefits and medical plan`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

Lors de la recherche d’un formulaire, l’utilisateur a fourni plus d’informations que nécessaire pour trouver le formulaire. Les informations supplémentaires sont retournées en tant que **builtin.keyPhrase**. L’application client peut utiliser ces informations supplémentaires pour une question de suivi, telle que « Souhaitez-vous contacter un représentant des Ressources humaines à propos de la couverture dentaire » ou proposer un menu avec plus d’options, y compris « Plus d’informations sur la nouvelle couverture dentaire ou les remboursements médicaux ».

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, avec la détection d’entité keyPhrase, a identifié une intention de type requête en langage naturel et retourné les données extraites contenant le thème principal. 

À présent, votre chatbot possède suffisamment d’informations pour déterminer l’étape suivante de la conversation. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette demande. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité keyPhrase de l’énoncé pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter l’analyse des sentiments à une application](luis-quickstart-intent-and-sentiment-analysis.md)