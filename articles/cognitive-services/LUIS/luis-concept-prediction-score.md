---
title: Comprendre le score de prédiction retourné par LUIS - Azure | Documents Microsoft
description: Découvrez ce que signifie le score de prédiction dans LUIS
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: cee7243531857f07dec2e968352ffb54aef16bf1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224584"
---
# <a name="prediction-score"></a>Score de prédiction
Un score de prédiction indique le degré de confiance que LUIS a dans les résultats de prédiction. 

Un score de prédiction est généralement compris entre zéro (0) et un (1). Un exemple de score LUIS de grande confiance est égal à 0,99. Un exemple de score faible confiance est égal à 0,01. 

|Valeur du score|Confiance|
|--|--|
|1|correspondance définie|
|0,99|grande confiance|
|0.01|faible confiance|
|0|échec définitif de correspondance|

Lorsqu’un énoncé obtient un score à faible degré de confiance, LUIS le signale sur la page **Intention** du site web [LUIS](luis-reference-regions.md), en encadrant **labeled-intent** en rouge. 

![Disparité de score](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Intention de score principale
Chaque prédiction d’énoncé retourne une intention de score principale. Il s’agit d’une comparaison numérique des scores de prédiction. La différence entre les deux principaux scores peut être très faible. LUIS n’indique pas cette proximité autrement qu’en retournant des scores.  

Si vous êtes inquiet de la proximité des scores principaux, vous devez retourner le score pour toutes les intentions. Vous pouvez soit ajouter aux deux intentions des énoncés qui indiquent leurs différences par le biais du choix et de l’ordre des mots, ou bien l’application appelant LUIS peut, telle un chatbot, faire des choix de programmation quant à la manière de gérer les deux intentions principales. 

## <a name="return-prediction-score-for-all-intents"></a>Retourner le score de prédiction pour toutes les intentions
Un résultat de test ou de point de terminaison peut inclure toutes les intentions. Cette configuration est définie sur le [point de terminaison](https://aka.ms/v1-endpoint-api-docs) avec la `verbose=true` paire nom/valeur de chaîne de requête. 

## <a name="review-intents-with-similar-scores"></a>Passer en revue les intentions aux scores similaires
La révision du score pour toutes les intentions est une bonne manière de vérifier non seulement que l’intention correcte est identifiée, mais encore, que le score d’intention suivant identifié est nettement plus faible et cohérent par rapport aux énoncés. 

Si plusieurs intentions ont des scores de prédiction proches, en se basant sur le contexte d’un énoncé, LUIS peut basculer d’une intention à l’autre. Pour résoudre ce problème, continuez à ajouter des énoncés à chaque intention avec une plus grande variété de différences contextuelles.   

## <a name="e-exponent-notation"></a>Notation E (exposant)

Les scores de prédiction peuvent utiliser la notation d’exposant, *apparaissant* au-dessus de la plage 0-1, par exemple `9.910309E-07`. Ce score indique un très **petit** nombre.

|Score de notation E |Score réel|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Différences par rapport aux prédictions
Lorsque vous effectuez l’apprentissage du même modèle dans une autre application et que les scores ne sont pas les mêmes, c’est dû au fait qu’il existe un élément de caractère aléatoire dans l’apprentissage. En second lieu, un chevauchement d’énoncé sur plus d’une intention signifie que l’intention principale pour le même énoncé peut changer en fonction de l’apprentissage.

Si votre chatbot requiert un score LUIS spécifique pour indiquer la confiance dans un énoncé, à la place, vous devez utiliser la différence de score entre les deux intentions principales. Ceci offre une flexibilité pour varier l’apprentissage. 

## <a name="punctuation"></a>Ponctuation
La ponctuation est un jeton distinct dans LUIS. Un énoncé qui se termine par un point et un énoncé qui n’en comporte pas sont deux énoncés distincts, qui sont susceptibles d’obtenir deux prédictions différentes. Veillez à ce que le modèle gère la ponctuation soit dans les [exemples d’énoncés](luis-concept-utterance.md) (avec ou sans ponctuation), soit dans les [modèles}(luis-concept-patterns.md) où il est plus facile d’ignorer la ponctuation avec la syntaxe spéciale : `I am applying for the {Job} position[.]`.

## <a name="next-steps"></a>Étapes suivantes

Consulter [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.