---
title: Tester votre application LUIS
titleSuffix: Language Understanding - Azure Cognitive Services
description: Le test est le processus consistant à fournir des exemples d’énoncés à LUIS et obtenir une réponse des intentions et des entités reconnues par LUIS.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: c9f1cf80cd3a781e878daca2048f7c5dc9095a7c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789534"
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

Les énoncés dont LUIS n’est pas sûr sont répertoriés dans la page **[Réviser les énoncés de point de terminaison](luis-how-to-review-endpoint-utterances.md)** du site web [LUIS](luis-reference-regions.md). 

![Réviser les énoncés de point de terminaison](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Penser à former
Pensez à [former](luis-how-to-train.md) LUIS après avoir apporté des modifications au modèle. Les modifications apportées à l’application LUIS ne sont pas visibles dans le test avant que l’application ait été formée. 

## <a name="best-practices"></a>Bonnes pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [test](luis-interactive-test.md) de vos énoncés.
