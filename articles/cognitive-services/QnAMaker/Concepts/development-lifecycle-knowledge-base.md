---
title: Cycle de vie d’une base de connaissances – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3f78b8a2566137d596f4ab3f083e1d14289365c3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684019"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cycle de vie d’une base de connaissances dans QnA Maker
QnA Maker apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison. 

![Cycle de création](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Création d’une base de connaissances QnA Maker
Le point de terminaison de la base de connaissances QnA Maker recherche une réponse correspondent le mieux à une requête utilisateur dans le contenu de la base de connaissances. La création d’une base de connaissances s’effectue en une seule fois. Elle consiste à créer un référentiel contenant des questions, des réponses et des métadonnées associées. Vous pouvez créer une base de connaissances en y intégrant du contenu existant, tel que des pages FAQ, des manuels de produits ou des paires structurées de questions-réponses. Découvrez comment [créer une base de connaissances](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Test et mise à jour de la base de connaissances

La base de connaissances peut être testée dès qu’elle contient des données, qui ont été ajoutées manuellement ou par le biais d’une extraction automatique. Test interactive peut être effectué dans le portail QnA Maker via le **Test** panneau en entrant des requêtes utilisateur courantes et en vérifiant que les réponses retournées avec la réponse correcte et le score de confiance suffisant. 

* **Pour corriger les scores de confiance faible**: ajouter d’autres questions. 
* **Lorsqu’une requête retourne incorrectement le [réponse par défaut](confidence-score.md#change-default-answer)**: ajouter de nouvelles réponses à la question correcte. 

Poursuivez cette boucle de test-mise à jour jusqu’à ce que vous soyez satisfait des résultats. Découvrez comment [tester votre base de connaissances](../How-To/test-knowledge-base.md).

Pour les grandes bases de connaissances, utilisez les tests automatisés avec le [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) et le `isTest` corps de la propriété qui interroge le `test` base de connaissances au lieu de la base de connaissances publiée. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publier la base de connaissances
Une fois que vous avez testé la base de connaissances, vous pouvez la publier. Le processus de publication envoie (push) la dernière version de la base de connaissances testée à un index de Recherche Azure dédié, qui représente la base de connaissances **publiée**. Il crée également un point de terminaison qui peut être appelé dans votre application ou bot conversationnel.

De cette manière, les modifications apportées à la version de test de la base de connaissances n’impactent pas la version publiée qui est potentiellement utilisée dans une application de production.

Chacune de ces bases de connaissances peut être ciblée séparément pour le test. À l’aide de l’API, vous pouvez cibler la version de test de la base de connaissances avec `isTest` corps de la propriété dans l’appel de generateAnswer.

Découvrez comment [publier votre base de connaissances](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Surveiller l’utilisation
Pour pouvoir enregistrer les journaux d’activité de conversations du service, vous devez activer Application Insights quand vous [créez votre service QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Cela vous permet d’obtenir diverses analytiques sur l’utilisation de votre service. Découvrez comment utiliser les insights de l’application pour obtenir des [analytiques sur votre service QnA Maker](../How-To/get-analytics-knowledge-base.md).

Selon ce que vous apprenez des analytiques, effectuez les [mises à jour appropriées pour votre base de connaissances](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Score de confiance](./confidence-score.md)

## <a name="see-also"></a>Voir aussi 

[Base de connaissances](./knowledge-base.md)
[Vue d’ensemble de QnA Maker](../Overview/overview.md)
