---
title: Révision des énoncés de point de terminaison
titleSuffix: Azure Cognitive Services
description: Améliorez les prédictions de l’application en vérifiant ou corrigeant les énoncés reçus par le point de terminaison HTTP de LUIS dont ce dernier n’est pas sûr. Certains énoncés peuvent devoir faire l’objet d’une vérification d’intention, d’autres d’une vérification d’entité.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 2994f7b19d5a104b129dc4d7aff29dabbc89f0f4
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276013"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Didacticiel : Corriger les prédictions incertaines en révisant les énoncés de point de terminaison
Dans ce tutoriel, améliorez les prédictions de l’application en vérifiant ou corrigeant les énoncés reçus par le point de terminaison HTTPS de LUIS dont ce dernier n’est pas sûr. Certains énoncés peuvent devoir faire l’objet d’une vérification d’intention, d’autres d’une vérification d’entité. Vous devez examiner les énoncés de point de terminaison régulièrement dans le cadre de la maintenance LUIS planifiée. 

Ce processus de révision est une autre façon pour LUIS de découvrir le domaine de votre application. LUIS a sélectionné les énoncés qui apparaissent dans la liste de révision. Cette liste est :

* Spécifique à l’application.
* Est destinée à améliorer la précision de prédiction de l’application. 
* Doit être révisée régulièrement. 

En passant en revue les énoncés de point de terminaison, vous vérifiez ou corrigez l’intention prédite de l’énoncé. Vous étiquetez également des entités personnalisées non prédites ou incorrectement prédites. 

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer l’exemple d’application
> * Réviser les énoncés de point de terminaison
> * Mettre à jour une liste d’expressions
> * Entraîner une application
> * Publier une application
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importer l’exemple d’application

Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Procédez comme suit :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

1. Importez le code JSON dans une nouvelle application.

1. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `review`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

1. Entraînez et publiez la nouvelle application.

1. Utilisez le point de terminaison pour ajouter les énoncés suivants. Vous pouvez le faire avec un [script](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) ou à partir du point de terminaison dans un navigateur. Les énoncés à ajouter sont :

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Si vous disposez de toutes les versions de l’application, grâce à la série de didacticiels, vous serez surpris de constater que la liste **Réviser les énoncés de point de terminaison** ne change pas, selon la version. Il existe un seul pool d’énoncés à réviser, quelle que soit la version que vous modifiez ou la version de l’application publiée au point de terminaison. 

## <a name="review-endpoint-utterances"></a>Réviser les énoncés de point de terminaison

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Sélectionnez **Réviser les énoncés de point de terminaison** dans le volet de navigation gauche. La liste est filtrée pour intention **ApplyForJob**. 

    [![Capture d’écran du bouton Réviser les énoncés de point de terminaison dans le volet de navigation gauche](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

1. Appuyez sur **Affichage des entités** pour afficher les entités étiquetées. 
    
    [![Capture d’écran de Réviser les énoncés de point de terminaison avec le bouton Affichage des entités en surbrillance](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)


    Ce énoncé, `I'm looking for a job with Natural Language Processing`, n’a pas la bonne intention. 

    La raison pour laquelle l’énoncé a été mal prédit est que l’intention **ApplyForJob** a 21 énoncés et que **GetJobInformation** en a sept. L’intention ayant le plus grand nombre d’énoncés aura une prédiction plus élevée. Il est important que la quantité et la qualité des énoncés parmi les intentions soient équilibrées.

1.  Pour aligner cet énoncé, sélectionnez l’intention correcte et marquez l’entité Job au sein de celle-ci. Ajoutez l’énoncé modifié à l’application en cochant la case verte. 

    |Énoncé|Intention correcte|Entités manquantes|
    |:--|:--|:--|
    |`I'm looking for a job with Natural Language Processing`|GetJobInfo|Travail - « Traitement du langage naturel »|

    L’ajout de l’énoncé déplace l’énoncé de **Réviser les énoncés de point de terminaison** à l’intention **GetJobInformation**. L’énoncé de point de terminaison est désormais un énoncé d’exemple pour cette intention. 

    Avec l’alignement correct de cet énoncé, d’autres énoncés doivent être ajoutés à l’intention **GetJobInformation**. Ceci est considéré comme un exercice que vous devez effectuer vous-même. Chaque intention, à l’exception de l’intention **None**, doit avoir à peu près le même nombre d’énoncés d’exemple. L’intention **None** doit avoir 10 % du nombre total d’énoncés de l’application. 

1. Révisez les énoncés restants dans cette intention, en étiquetant les énoncés et en corrigeant l’**Intention alignée**, si ceux-ci sont incorrects.

1. La liste ne devrait plus avoir ces énoncés. Si d’autres énoncés apparaissent, continuez d’examiner la liste, en corrigeant les intentions et en étiquetant toutes les entités manquantes, jusqu’à ce que la liste soit vide. 

1. Sélectionnez l’intention suivante dans la liste Filtre, puis continuez à corriger les énoncés et à étiqueter les entités. N’oubliez pas que la dernière étape de chaque intention consiste à sélectionner **Ajouter à l’intention alignée** sur la ligne de l’énoncé ou à cocher la case à côté de chaque intention et sélectionner **Ajouter la sélection** au-dessus de la table.

    Continuez jusqu’à ce que toutes les intentions et entités dans la liste de filtres aient une liste vide. C’est une application très petite. Le processus de révision ne prend que quelques minutes. 

## <a name="update-phrase-list"></a>Mettre à jour une liste d’expressions
Conservez la liste d’expressions actuelle avec tout nom de tâche récemment découvert. 

1. Sélectionnez **Liste d’expressions** dans le volet de navigation gauche.

2. Sélectionnez la liste d’expressions **Jobs**.

3. Ajoutez `Natural Language Processing` en tant que valeur, puis sélectionnez **Enregistrer**. 

## <a name="train"></a>Former

LUIS ne connaît pas les modifications apportées tant que son apprentissage n’a pas été effectué. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

Si vous avez importé cette application, vous devez sélectionner **Analyse des sentiments**.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

Essayez un énoncé proche de l’énoncé corrigé. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Are there any natural language processing jobs in my department right now?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

   ```json
   {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
   }
   }
   ```

   L’intention correcte a été prédite avec un score élevé et l’entité **Jobs** est détectée en tant que `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>La révision peut-elle être remplacée en ajoutant plus d’énoncés ? 
Vous vous demandez sûrement pourquoi ne pas ajouter plus d’énoncés d’exemple. Quel est le but de la révision des énoncés de point de terminaison ? Dans une application LUIS réaliste, les énoncés de point de terminaison émanent d’utilisateurs avec des choix de mots et une disposition que vous n’avez pas encore utilisés. Si vous aviez utilisé le même choix de mots et la disposition, la prédiction d’origine aurait un pourcentage plus élevé. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Pourquoi la meilleure intention est-elle sur la liste des énoncés ? 
Certains des énoncés de point de terminaison auront un score de prédiction élevé dans la liste de révision. Vous devez quand même réviser et vérifier ces énoncés. Ils figurent dans la liste car l’intention suivante la plus haute avait un score trop proche de celui de la meilleure intention. Vous souhaitez une différence d’environ 15 % entre les deux meilleures intentions.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez révisé les énoncés envoyés au point de terminaison et dont LUIS n’était pas sûr. Une fois ces énoncés vérifiés et déplacés vers les intentions correctes en tant qu’exemples d’énoncés, LUIS améliore la précision des prédictions.

> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de ces modèles](luis-tutorial-pattern.md)
