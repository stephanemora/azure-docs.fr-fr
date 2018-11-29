---
title: 'Tutoriel 8 : Extraction d’expressions clés dans LUIS'
titleSuffix: Azure Cognitive Services
description: Utilisez l’entité prédéfinie keyPhrase pour extraire le thème principal des énoncés. Vous n’avez pas besoin d’étiqueter les énoncés avec des entités prédéfinies. L’entité est détectée automatiquement.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: fafb6a6cb986b5bcd795c9bac0fadfce2827c667
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424810"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>Tutoriel 8 : Extraire des expressions clés d’un énoncé
Dans ce tutoriel, vous utilisez l’entité keyPhrase prédéfinie pour extraire le thème principal des énoncés. Vous n’avez pas besoin d’étiqueter les énoncés avec des entités prédéfinies. L’entité est détectée automatiquement.

Les énoncés suivants présentent des exemples d’expressions clés :

|Énoncé|Valeurs d’entité keyPhrase|
|--|--|
|Y-a-t-il un nouveau régime d’assurance-maladie qui propose une franchise moins élevée l’année suivante ?|« franchise moins élevée »<br>« nouveau régime d’assurance-maladie »<br>« année »|
|La thérapie visuelle est-elle couverte par le régime d’assurance-maladie à franchise élevée ?|« régime d’assurance-maladie à franchise élevée »<br>« thérapie visuelle »|

Votre application cliente peut utiliser ces valeurs, ainsi que d’autres entités extraites, pour décider de l’étape suivante de la conversation.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante
> * Ajouter une entité keyPhrase 
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante

Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)**, sous l’onglet **Versions**, clonez la version et nommez-la `keyphrase`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="add-keyphrase-entity"></a>Ajouter une entité keyPhrase 
Ajoutez une entité prédéfinie keyPhrase pour extraire le thème des énoncés.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Dans le menu gauche, sélectionnez **Entités**.

3. Sélectionnez **Ajouter une entité prédéfinie**.

4. Dans la boîte de dialogue contextuelle, sélectionnez **keyPhrase**, puis **Terminé**. 

    [ ![Capture d’écran de la boîte de dialogue contextuelle Entités](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. Sélectionnez **Intentions** dans le menu de gauche, puis sélectionnez l’intention **Utilities.Confirm**. L’entité keyPhrase est étiquetée dans plusieurs énoncés. 

    [ ![Capture d’écran de l’intention Utilities.Confirm avec keyPhrases étiquetées dans les énoncés](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `does form hrf-123456 cover the new dental benefits and medical plan`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 
    
    ```JSON
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

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté l’entité keyPhrase prédéfinie, en fournissant rapidement des expressions clés dans des énoncés sans avoir à étiqueter ces derniers. 

> [!div class="nextstepaction"]
> [Ajouter l’analyse des sentiments à une application](luis-quickstart-intent-and-sentiment-analysis.md)
