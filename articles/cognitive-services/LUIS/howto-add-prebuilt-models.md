---
title: Modèles prédéfinis pour Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS inclut un ensemble de modèles prédéfinis pour ajouter rapidement des scénarios de conversation utilisateur courants.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506784"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Modèles prédéfinis pour les scénarios d’usage courants 

LUIS inclut un ensemble d’intentions prédéfinies à partir des domaines prédéfinis pour ajouter rapidement des intentions et des énoncés courants. Il s’agit d’un moyen simple et rapide d’ajouter des fonctionnalités à votre application cliente conversationnelle sans avoir à concevoir les modèles pour ces fonctionnalités. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Ajouter un domaine prédéfini

1. Sur la page **My Apps** (Mes applications), sélectionnez votre application. Vous ouvrez ainsi votre application à la section **Build** (Générer). 

1. Sur la page **Intentions**, sélectionnez **Ajouter des domaines prédéfinis** depuis la barre d’outils en bas à gauche. 

1. Sélectionnez l’intention **Calendrier**, puis sélectionnez le bouton **Ajouter un domaine**.

    ![Ajouter le domaine prédéfini Calendrier](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Sélectionnez **Intentions** dans le volet de navigation gauche pour afficher les intentions Calendrier. Chaque intention de ce domaine comporte le préfixe `Calendar.`. Deux entités et des énoncés pour ce domaine sont ajoutés à l’application : `Calendar.Location` et `Calendar.Subject`. 

### <a name="train-and-publish"></a>Former et publier

1. Après avoir ajouté le domaine, effectuez l’apprentissage de l’application en sélectionnant **Effectuer l’apprentissage** dans la barre d’outils en haut à droite. 

1. Dans la barre d’outils supérieure, sélectionnez **Publier**. Choisissez l’emplacement de publication **Production**. 

1. Lorsque la notification de réussite de couleur verte s’affiche, sélectionnez le lien **Refer to the list of endpoints** (Consulter la liste des points de terminaison) pour visualiser les points de terminaison.

1. Sélectionnez un point de terminaison. Un nouvel onglet de navigateur s’ouvre au niveau de ce point de terminaison. Laissez l’onglet ouvert et poursuivez à la section **Test**.

### <a name="test"></a>Test

Testez la nouvelle intention au niveau du point de terminaison en ajoutant une valeur pour le paramètre **q** : `Schedule a meeting with John Smith in Seattle next week`.

LUIS retourne l’intention correcte et le sujet de réunion :

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>Ajouter une intention prédéfinie

1. Sur la page **My Apps** (Mes applications), sélectionnez votre application. Vous ouvrez ainsi votre application à la section **Build** (Générer). 

1. Sur la page **Intents** (Intentions), sélectionnez **Add prebuilt intent** (Ajouter une intention prédéfinie) à partir de la barre d’outils au-dessus de la liste des intentions. 

1. Sélectionnez l’intention **Utilities.Cancel** à partir de la boîte de dialogue contextuelle. 

    ![Ajouter une intention prédéfinie](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Cliquez sur le bouton **Terminé**.

### <a name="train-and-test"></a>Effectuer l’apprentissage et tester

1. Après avoir ajouté l’intention, effectuez l’apprentissage de l’application en sélectionnant **Effectuer l’apprentissage** dans la barre d’outils en haut à droite. 

1. Testez la nouvelle intention en sélectionnant **Tester** dans la barre d’outils de droite. 

1. Dans la zone de texte, entrez des énoncés d’annulation :

    |Énoncé de test|Scores de prédictions|
    |--|:--|
    |Je souhaite annuler mon vol.|0.67|
    |Annuler l’achat.|0.52|
    |Annuler la réunion.|0.56|

    ![Tester l’intention prédéfinie](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Ajouter une entité prédéfinie

1. Ouvrez votre application en cliquant sur son nom dans la page **Mes applications**, puis cliquez sur **Entités** du côté gauche. 

1. Dans la page **Entities**, cliquez sur **Add prebuilt entity** (Ajouter une entité prédéfinie).

1. Dans la boîte de dialogue **Add prebuilt entities** (Ajouter des entités prédéfinies), sélectionnez l’entité prédéfinie datetimeV2. 

    ![Boîte de dialogue Add prebuilt entities (Ajouter des entités prédéfinies)](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Sélectionnez **Terminé**. Une fois l’entité ajoutée, vous n’avez pas besoin d’effectuer l’apprentissage de l’application. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publier pour voir le modèle prédéfini à partir du point de terminaison de prédiction

La manière la plus simple de visualiser la valeur d’un modèle prédéfini consiste à interroger cette valeur à partir du point de terminaison publié. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Marquage des entités contenant un jeton d’entité prédéfinie
 Si vous avez du texte, tel que `HH-1234`, que vous souhaitez marquer comme une entité personnalisée _et_ que vous avez l’[entité prédéfinie number](luis-reference-prebuilt-number.md) ajoutée au modèle, vous ne pouvez pas marquer l’entité personnalisée dans le portail LUIS. Vous pouvez la marquer avec l’API. 

 Pour marquer ce type de jeton, où une partie est déjà marquée avec une entité prédéfinie, supprimez l’entité prédéfinie de l’application LUIS. Vous n’avez pas besoin d’entraîner l’application. Marquez le jeton avec votre propre entité personnalisée. Ensuite, rajoutez l’entité prédéfinie à l’application LUIS.

 En guise d’autre exemple, considérez l’énoncé comme une liste de préférences de classes : `I want first year spanish, second year calculus, and fourth year english lit.` Si l’application LUIS a l’entité prédéfinie ordinal ajoutée, `first`, `second` et `fourth` seront déjà marquées avec des ordinaux. Si vous souhaitez capturer l’ordinal et la classe, vous pouvez créer une entité composite et l’envelopper autour de l’entité prédéfinie ordinal et de l’entité personnalisée pour le nom de la classe.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Générer un modèle à partir d’un fichier .csv avec des API REST](./luis-tutorial-node-import-utterances-csv.md)
