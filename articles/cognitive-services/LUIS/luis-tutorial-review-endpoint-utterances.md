---
title: Didacticiel pour réviser les énoncés de point de terminaison dans Language Understanding (LUIS) - Azure | Microsoft Docs
description: Dans ce didacticiel, découvrez comment passer en revue les énoncés de point de terminaison dans le domaine de ressources humaines (RH) dans LUIS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: v-geberr
ms.openlocfilehash: cd8374b3804594f96212dbe741f99ba22d33a4e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970504"
---
# <a name="tutorial-review-endpoint-utterances"></a>Didacticiel : réviser les énoncés de point de terminaison
Dans ce didacticiel, améliorez les prédictions de l’application en vérifiant ou corrigeant les énoncés reçus par le point de terminaison HTTP de LUIS. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre la révision des énoncés de point de terminaison 
> * Utiliser l’application LUIS pour le domaine des ressources humaines (RH) 
> * Réviser les énoncés de point de terminaison
> * Entraîner et publier l’application
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

Pour cet article, vous devez disposer d’un compte [LUIS](luis-reference-regions.md#luis-website) gratuit afin de créer votre application LUIS.

## <a name="before-you-begin"></a>Avant de commencer
Si vous n’avez pas l’application des ressources humaines à partir du didacticiel des [sentiments](luis-quickstart-intent-and-sentiment-analysis.md), importez l’application à partir du référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json). Si vous utilisez ce didacticiel comme une nouvelle application importée, vous devez également effectuer l’apprentissage, publier, puis ajouter les énoncés au point de terminaison avec un [script](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) ou à partir du point de terminaison dans un navigateur. Les énoncés à ajouter sont :

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `review`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

Si vous disposez de toutes les versions de l’application, grâce à la série de didacticiels, vous serez surpris de constater que la liste **Réviser les énoncés de point de terminaison** ne change pas, selon la version. Il existe un seul pool d’énoncés à réviser, quelle que soit la version de l’énoncé que vous modifiez ou la version de l’application publiée au point de terminaison. 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>Objectif de la révision des énoncés de point de terminaison
Ce processus de révision est une autre façon pour LUIS de découvrir le domaine de votre application. LUIS a sélectionné les énoncés dans la liste de révision. Cette liste est :

* Spécifique à l’application.
* Est destinée à améliorer la précision de prédiction de l’application. 
* Doit être révisée régulièrement. 

En passant en revue les énoncés de point de terminaison, vous vérifiez ou corrigez l’intention prédite de l’énoncé. Vous étiquetez également des entités personnalisées non prédites. 

## <a name="review-endpoint-utterances"></a>Réviser les énoncés de point de terminaison

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

    [ ![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. Sélectionnez **Réviser les énoncés de point de terminaison** dans le volet de navigation gauche. La liste est filtrée pour intention **ApplyForJob**. 

    [ ![Capture d’écran du bouton Réviser les énoncés de point de terminaison dans le volet de navigation gauche](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. Appuyez sur **Affichage des entités** pour afficher les entités étiquetées. 
    
    [ ![Capture d’écran de Réviser les énoncés de point de terminaison avec le bouton Affichage des entités en surbrillance](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |Énoncé|Intention correcte|Entités manquantes|
    |:--|:--|:--|
    |Je recherche un travail dans le traitement du langage naturel|GetJobInfo|Travail - « Traitement du langage naturel »|

    Cet énoncé n’est pas dans l’intention correcte et a un score inférieur à 50 %. L’intention **ApplyForJob** a 21 énoncés par rapport aux sept énoncés dans **GetJobInformation**. Avec l’alignement correct de l’énoncé de point de terminaison, d’autres énoncés doivent être ajoutés à l’intention **GetJobInformation**. Ceci est considéré comme un exercice que vous devez effectuer vous-même. Chaque intention, à l’exception de l’intention **None**, doit avoir à peu près le même nombre d’énoncés d’exemple. L’intention **None** doit avoir 10 % du nombre total d’énoncés de l’application. 

    Lorsque vous êtes dans **Tokens View** (Vue du jeton), vous pouvez pointer sur n’importe quel texte en bleu de l’énoncé pour afficher le nom prédit de l’entité. 

3. Pour l’intention `I'm looking for a job with Natual Language Processing`, sélectionnez l’intention correcte, **GetJobInformation** dans la colonne **Intention alignée**. 

    [ ![Capture d’écran de Réviser les énoncés de point de terminaison alignant l’énoncé à l’intention](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. Dans le même énoncé, l’entité pour `Natural Language Processing` est keyPhrase. Ce devrait être une entité **Job** à la place. Sélectionnez `Natural Language Processing` puis sélectionnez l’entité **Job** dans la liste.

    [ ![Capture d’écran de Réviser les énoncés de point de terminaison étiquetant l’entité en énoncé](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. Sur la même ligne, sélectionnez sur la coche encerclée dans la colonne **Ajouter à l’intention alignée**. 

    [ ![Capture d’écran de la finalisation de l’alignement de l’énoncé dans l’intention](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Cette action déplace l’énoncé de **Réviser les énoncés de point de terminaison** à l’intention **GetJobInformation**. L’énoncé de point de terminaison est désormais un énoncé d’exemple pour cette intention. 

6. Révisez les énoncés restants dans cette intention, en étiquetant les énoncés et en corrigeant l’**Intention alignée**, si ceux-ci sont incorrects.

7. Lorsque tous les énoncés sont corrects, sélectionnez la case à cocher de chaque ligne, puis sélectionnez **Ajouter la sélection** pour aligner correctement les énoncés. 

    [ ![Capture d’écran de la finalisation des énoncés restants en intention alignée](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. La liste ne devrait plus avoir ces énoncés. Si d’autres énoncés apparaissent, continuez d’examiner la liste, en corrigeant les intentions et en étiquetant toutes les entités manquantes, jusqu'à ce qu’elle soit vide. Sélectionnez l’intention suivante dans la liste Filtre, puis continuez à corriger les énoncés et à étiqueter les entités. N’oubliez pas que la dernière étape de chaque intention consiste à sélectionner **Ajouter à l’intention alignée** sur la ligne de l’énoncé ou à cocher la case à côté de chaque intention et sélectionner **Ajouter la sélection** au-dessus de la table. 

    C’est une application très petite. Le processus de révision ne prend que quelques minutes.

## <a name="add-new-job-name-to-phrase-list"></a>Ajoutez un nouveau nom de tâche à la liste d’expressions
Conservez la liste d’expressions actuelle avec tout nom de tâche récemment découvert. 

1. Sélectionnez **Liste d’expressions** dans le volet de navigation gauche.

2. Sélectionnez la liste d’expressions **Jobs**.

3. Ajoutez `Natural Language Processing` en tant que valeur, puis sélectionnez **Enregistrer**. 

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS
LUIS ne connaît pas les modifications apportées tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Former**.

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir un modèle à jour de l’application LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

2. Si vous avez importé cette application, vous devez sélectionner **Analyse des sentiments**. 

3. Sélectionnez l’emplacement Production et le bouton **Publier**.

4. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-an-utterance"></a>Interroger le point de terminaison avec un énoncé
Essayez un énoncé proche de l’énoncé corrigé. 

1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Are there any natural language processing jobs in my department right now?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

```JSON
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
Certains des énoncés de point de terminaison auront un pourcentage élevé dans la liste de révision. Vous devez quand même réviser et vérifier ces énoncés. Ils figurent dans la liste car l’intention suivante la plus haute avait un score trop proche de celui de la meilleure intention. 

## <a name="what-has-this-tutorial-accomplished"></a>Qu’a accompli ce didacticiel ?
La précision de cette prédiction d’application a augmenté en révisant des énoncés du point de terminaison. 

## <a name="clean-up-resources"></a>Supprimer les ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Sélectionnez **Mes applications** dans le menu en haut à gauche. Sélectionnez les points de suspension **...** à droite du nom de l’application dans la liste des applications, sélectionnez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de ces modèles](luis-tutorial-pattern.md)
