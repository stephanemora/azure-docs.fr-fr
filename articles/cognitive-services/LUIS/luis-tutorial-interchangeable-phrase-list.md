---
title: 'Tutoriel : utiliser une liste d’expressions pour améliorer les prédictions de LUIS – Azure | Microsoft Docs'
description: Dans ce tutoriel, nous ajouterons une liste d’expressions à une application LUIS et observerons l’amélioration du score.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868971"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Tutoriel : ajouter une liste d’expressions pour améliorer les prédictions
Dans ce didacticiel, nous améliorerons la précision des scores intentionnels et identifierons des entités pour les mots qui ont la même signification (synonymes) en ajoutant une [fonctionnalité de liste d’expressions](./luis-concept-feature.md) interchangeables.

> [!div class="checklist"]
* Importer une nouvelle application  
* Interroger un point de terminaison avec un énoncé connu 
* Interroger un point de terminaison avec un énoncé _inconnu_
* Ajouter la liste d’expressions pour améliorer le score des énoncés inconnus
* Vérifier l’entité détectée lors de l’utilisation de la liste d’expressions

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="import-a-new-app"></a>Importer une nouvelle application
1. Téléchargez le [modèle d’application LUIS][LuisSampleApp] conçu pour ce tutoriel. Vous en aurez besoin à la prochaine étape. 

2. Comme décrit dans [Créer une application](Create-new-app.md#import-new-app), importez le fichier que vous avez téléchargé dans le site web [LUIS][LUIS] comme nouvelle application. Le nom de l’application est « Didacticiel Ma liste d’expressions ». Elle contient des intentions, des entités et des énoncés. 

3. [Effectuer la formation](luis-how-to-train.md) de votre application. Jusqu'à ce qu’elle soit formée, vous ne pouvez pas la [tester de façon interactive](interactive-test.md#interactive-testing) sur le site web [LUIS][LUIS]. 

4. Sur la page [Publier](luis-how-to-publish-app.md), cochez la case **Inclure tous les scores intentionnels prédits**. Lorsque la case à cocher est activée, toutes les intentions sont retournées. Lorsque la case à cocher est désactivée, seule l’intention principale est retournée. 

5. [Publier](luis-how-to-publish-app.md) l’application. Publier l’application vous permet de la tester à l’aide du point de terminaison HTTPS. 

## <a name="test-a-trained-utterance"></a>Tester un énoncé formé
Utilisez le point de terminaison publié pour interroger un énoncé que l’application connaît déjà. Étant donné que LUIS connaît déjà l’énoncé, le score est élevé, et l’entité est détectée.

1. Sur le site web [Language Understanding (LUIS)] [ LUIS], sur la page **Publier** de la nouvelle application, sélectionnez l’URL de point de terminaison dans la section **Ressources et clés**. 

    ![Publiez l’URL de point de terminaison](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. Dans le navigateur, à la fin de l’URL, ajoutez la requête suivante après `q=`.

    `I want a computer replacement`

    Le point de terminaison répond avec le texte JSON suivant :
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    Le score d’intention de 0.973 et le score de détection d’entité de 0.846 sont élevés, car l’application a été formée avec cet énoncé. L’énoncé est dans l’application LUIS, à la page des intentions pour **GetHardware**. Le texte de l’énoncé, `computer`, est étiqueté en tant qu’entité **Matériel**. 
    
    |Statut|Word| Score d’intention | Score d’entité |
    |--|--|--|--|
    |Formé| vouloir | 0,973 | 0,846 |
    
    
## <a name="test-an-untrained-utterance"></a>Tester un énoncé non formé
Dans le navigateur, utilisez le même point de terminaison publié pour interroger avec un énoncé que l’application ne connaît pas encore :

`I require a computer replacement`

Cet énoncé utilise un synonyme de l’énoncé précédent :

| Mot ayant formé | Synonyme non formé |
|--|--|
| vouloir | exiger |

La réponse du point de terminaison est la suivante :

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| Statut | Word | Score d’intention | Score d’entité |
|--|--|--|--|
| Formé| vouloir | 0,973 | 0,846 |
| Non formé| exiger | 0,840 | - |

Le score d’intention de l’énoncé non formé est inférieur à celui de l’énoncé étiqueté, car LUIS sait que la phrase est grammaticalement la même. Mais LUIS ne sait pas que les énoncés ont la même signification. L’entité **Matériel** n’est donc pas trouvée sans la liste d’expressions.

Vous devez apprendre à LUIS que *vouloir*  et *exiger* ont la même signification dans ce domaine d’application, car un mot peut avoir plusieurs significations. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Améliorer le score d’énoncé non formé avec la liste d’expressions 
1. Ajouter une fonctionnalité de [liste d’expressions](luis-how-to-add-features.md) nommée **vouloir** avec la valeur de `want`, puis sélectionnez **Entrer**.

    > [!TIP]
    > Après chaque mot ou une expression, sélectionnez la touche **Entrer**. Le mot ou l’expression sont ajoutés à la zone des **Valeurs de la liste d’expressions** pendant que le curseur reste dans la zone **Valeur**. Cette fonctionnalité permet d’entrer rapidement plusieurs valeurs.

2. Pour afficher les mots recommandés par LUIS, sélectionnez **Recommander**. 

    ![Valeurs recommandées](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Ajouter tous les mots. Si `require` est pas dans la liste recommandée, ajoutez-le comme valeur requise. 

4. Étant donné que ces termes sont synonymes, conservez le paramètre *interchangeable*, puis sélectionnez **Enregistrer**.

    ![Valeurs de la liste des expressions](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. Dans la barre de navigation supérieure, sélectionnez **Effectuer l'apprentissage** pour former l’application, mais ne la publiez pas. Vous avez maintenant deux modèles. Vous pouvez comparer des valeurs dans les deux modèles.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Comparer le modèle de liste d’expressions au modèle publié
Dans cette application, le modèle publié n’est pas formé avec les synonymes. Seul le modèle actuellement modifié inclut la liste d’expressions synonymes. Pour comparer les modèles, utilisez [test interactif](interactive-test.md#interactive-testing). 

1. Ouvrez le volet **Test** et entrez l’énoncé suivant :

    `I require a computer replacement`

2. Pour ouvrir le panneau d’inspection, sélectionnez **Inspecter**. 

    ![Sélectionnez inspecter](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Pour comparer le modèle publié au nouveau modèle de liste d’expressions, sélectionnez **Comparer avec la version publiée**.

    ![Inspecter la version publiée par rapport à la version actuelle courant](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Après avoir ajouté la liste d’expressions, la précision de l’énoncé est accrue et l’entité **Matériel** est trouvée. 

|Statut | Liste d’expressions| Score d’intention | Score d’entité |
|--|--|--|--|
| Publié | - | 0,84 | - |
| Modification en cours |✔| 0,92 | L’entité de matériel est identifiée |

> [!TIP]
> * Le [test interactif](interactive-test.md#interactive-testing) vous permet de comparer le modèle publié à n’importe quelles modifications formées effectuées après votre publication. 
> * Le [test du point de terminaison](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser) vous permet d’afficher le texte JSON exact de la réponse LUIS. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Obtenir le score d’entité avec le test de point de terminaison
Pour afficher le score de l’entité, [publiez le modèle](luis-how-to-publish-app.md) et interrogez le point de terminaison. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

L’entité **Matériel** affiche un score de 0,595 avec la liste d’expressions. Avant la création de la liste d’expressions, l’entité n’était pas détectée. 

|Statut | Liste d’expressions| Score d’intention | Score d’entité |
|--|--|--|--|
| Publié | - | 0,84 | - |
| Modification en cours |✔| 0,92 | 0,595 |


## <a name="clean-up-resources"></a>Supprimer les ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Pour cela, sélectionnez les points de suspension (***…***) à droite du nom de l’application dans la liste des applications, puis sélectionnez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir la prédiction d’énoncé avec une requête de point de terminaison](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
