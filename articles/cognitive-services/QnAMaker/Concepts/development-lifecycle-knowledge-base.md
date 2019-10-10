---
title: Cycle de vie d’une base de connaissances – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 530869928f7a25e779cb01f0fe392efdbb54c5ba
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695116"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cycle de vie d’une base de connaissances dans QnA Maker
QnA Maker apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison. 

![Cycle de création](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Création d’une base de connaissances QnA Maker
Le point de terminaison de la base de connaissances QnA Maker recherche une réponse correspondent le mieux à une requête utilisateur dans le contenu de la base de connaissances. La création d’une base de connaissances s’effectue en une seule fois. Elle consiste à créer un référentiel contenant des questions, des réponses et des métadonnées associées. Vous pouvez créer une base de connaissances en y intégrant du contenu existant, tel que des pages FAQ, des manuels de produits ou des paires structurées de questions-réponses. Découvrez comment [créer une base de connaissances](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Test et mise à jour de la base de connaissances

La base de connaissances peut être testée dès qu’elle contient des données, qui ont été ajoutées manuellement ou par le biais d’une extraction automatique. Vous pouvez effectuer un test interactif à partir du panneau **Test** du portail QnA Maker en entrant les requêtes utilisateur courantes et en vérifiant que les réponses retournées sont correctes et affichent un score de confiance suffisant. 

* **Pour améliorer les scores de confiance faibles** : ajoutez des questions alternatives. 
* **Si une requête retourne par erreur la [réponse par défaut](confidence-score.md#change-default-answer)**  : ajoutez de nouvelles réponses à la bonne question. 

Poursuivez cette boucle de test-mise à jour jusqu’à ce que vous soyez satisfait des résultats. Découvrez comment [tester votre base de connaissances](../How-To/test-knowledge-base.md).

Pour les grandes bases de connaissances, utilisez les tests automatisés avec l’[API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) et la propriété de corps `isTest` qui interroge la base de connaissances `test` au lieu de la base de connaissances publiée. 

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

Chacune de ces bases de connaissances peut être ciblée séparément pour le test. À l’aide des API, vous pouvez cibler la version de test de la base de connaissances en utilisant la propriété de corps `isTest` dans l’appel generateAnswer.

Découvrez comment [publier votre base de connaissances](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Surveiller l’utilisation
Pour pouvoir enregistrer les journaux d’activité de conversations du service, vous devez activer Application Insights quand vous [créez votre service QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Cela vous permet d’obtenir diverses analytiques sur l’utilisation de votre service. Découvrez comment utiliser les insights de l’application pour obtenir des [analytiques sur votre service QnA Maker](../How-To/get-analytics-knowledge-base.md).

Selon ce que vous apprenez des analytiques, effectuez les [mises à jour appropriées pour votre base de connaissances](../How-To/edit-knowledge-base.md).

## <a name="version-control-of-a-knowledge-base"></a>Gestion de versions d’une base de connaissances

La gestion de versions n’est pas fournie par QnA Maker. Vous devez exporter votre base de connaissances à partir de la page **Settings** (Paramètres) et utiliser votre propre méthodologie et vos propres outils.

Exportez la base de connaissances au format TSV ou XLS à partir de la page **Settings**. 

Si vous avez besoin de revenir à une version spécifique, vous devez importer ce fichier à partir de votre système local. Dans la page **Settings**, importez le fichier TSV ou XLS. Les questions et réponses qui se trouvent actuellement dans la base de connaissances sont remplacées par le contenu du fichier importé.   

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Score de confiance](./confidence-score.md)

## <a name="see-also"></a>Voir aussi 

[Base de connaissances](./knowledge-base.md)
[Vue d’ensemble de QnA Maker](../Overview/overview.md)
