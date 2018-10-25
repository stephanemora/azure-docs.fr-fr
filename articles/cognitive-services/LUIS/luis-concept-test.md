---
title: Tester votre application LUIS
titleSuffix: Azure Cognitive Services
description: Le test est le processus consistant à fournir des exemples d’énoncés à LUIS et obtenir une réponse des intentions et des entités reconnues par LUIS. Vous pouvez tester LUIS de manière interactive, un énoncé à la fois, ou fournir un lot d’énoncés. Avec les tests, vous comparez le modèle actif actuel au modèle publié.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 58bcf45d1a45d448f2f5845ffe43ad07f886a6fc
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637009"
---
# <a name="testing-example-utterances-in-luis"></a>Test d’exemples d’énoncés dans LUIS

Le test est le processus consistant à fournir des exemples d’énoncés à LUIS et obtenir une réponse des intentions et des entités reconnues par LUIS. 

Vous pouvez [tester](luis-interactive-test.md) LUIS de manière interactive, un énoncé à la fois, ou fournir un [lot](luis-concept-batch-test.md) d’énoncés. Avec les tests, vous comparez le modèle [actif](luis-concept-version.md#active-version) actuel au modèle publié. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Qu’est-ce qu’un score dans le test ?
Consultez les concepts du [Score de prédiction](luis-concept-prediction-score.md) pour en savoir plus sur les scores de prédiction.

## <a name="interactive-testing"></a>Test interactif
Le test interactif s’effectue à partir du panneau **Test** du site web. Vous pouvez entrer un énoncé pour voir comment les entités et les intentions sont identifiées et évaluées. Si LUIS ne prévoit pas les intentions et les entités comme prévu sur un énoncé dans le volet de test, copiez l’énoncé dans la page **Intention** en tant que nouvel énoncé. Puis, étiquetez les parties de cet énoncé et effectuez l’apprentissage de LUIS. 

## <a name="batch-testing"></a>Test par lot
Consultez [Test par lot](luis-concept-batch-test.md) si vous testez plusieurs énoncés à la fois.

## <a name="endpoint-testing"></a>Test des points de terminaison
Vous pouvez tester en utilisant le [point de terminaison](luis-glossary.md#endpoint) avec deux versions maximum de votre application. Avec la version principale ou live de votre application définie en tant que point de terminaison de **production**, ajoutez une deuxième version au point de terminaison **intermédiaire**. Cette approche vous offre trois versions d’un énoncé : le modèle actuel dans le volet Test du site web [LUIS](luis-reference-regions.md) et les deux versions sur les deux points de terminaison différents. 

Tous les tests sur votre point de terminaison sont inclus dans votre quota d’utilisation. 

## <a name="do-not-log-tests"></a>Ne pas enregistrer les tests
Si vous testez sur un point de terminaison et que vous ne souhaitez pas que l’énoncé soit enregistré, pensez à utiliser la configuration de la chaîne de requête `logging=false`.

## <a name="where-to-find-utterances"></a>Où trouver les énoncés
LUIS stocke tous les énoncés enregistrés dans le journal des requêtes, disponible au téléchargement sur le site web [LUIS](luis-reference-regions.md) à la page répertoriant les **Applications**, ainsi que les [API de création](https://aka.ms/luis-authoring-apis) LUIS. 

Les énoncés dont LUIS n’est pas sûr sont répertoriés dans la page **[Réviser les énoncés de point de terminaison](luis-how-to-review-endoint-utt.md)** du site web [LUIS](luis-reference-regions.md). 

![Réviser les énoncés de point de terminaison](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Penser à former
Pensez à [former](luis-how-to-train.md) LUIS après avoir apporté des modifications au modèle. Les modifications apportées à l’application LUIS ne sont pas visibles dans le test avant que l’application ait été formée. 

## <a name="best-practices"></a>Meilleures pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [test](luis-interactive-test.md) de vos énoncés.