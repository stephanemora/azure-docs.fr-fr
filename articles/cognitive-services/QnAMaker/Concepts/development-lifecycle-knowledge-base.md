---
title: Cycle de vie du développement d’une base de connaissances - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Cycle de vie du développement d’une base de connaissances
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 9ecdd2c7823eed145621b214690eff7681065507
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "35370925"
---
# <a name="knowledge-base-lifecycle"></a>Cycle de vie d’une base de connaissances
QnA Maker apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison. 

![Cycle de création](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Création d’une base de connaissances QnA Maker
Le point de terminaison de la base de connaissances QnA Maker recherche une réponse correspondent le mieux à une requête utilisateur dans le contenu de la base de connaissances. La création d’une base de connaissances s’effectue en une seule fois. Elle consiste à créer un référentiel contenant des questions, des réponses et des métadonnées associées. Vous pouvez créer une base de connaissances en y intégrant du contenu existant, tel que des pages FAQ, des manuels de produits ou des paires structurées de questions-réponses. Découvrez comment [créer une base de connaissances](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Test et mise à jour de la base de connaissances
La base de connaissances peut être testée dès qu’elle contient des données, qui ont été ajoutées manuellement ou par le biais d’une extraction automatique. Vous pouvez la tester à partir du panneau **Test**, en entrant les requêtes utilisateur courantes et en vérifiant que les réponses retournées sont appropriées et présentent un score de confiance correct. Vous pouvez ajouter des questions alternatives pour améliorer les scores de confiance faibles. Vous pouvez également ajouter de nouvelles réponses quand une requête retourne la réponse par défaut « aucune correspondance trouvée dans la base de connaissances ». Poursuivez cette boucle de test-mise à jour jusqu’à ce que vous soyez satisfait des résultats. Découvrez comment [tester votre base de connaissances](../How-To/test-knowledge-base.md).

Le test des bases de connaissances volumineuses peut être automatisé à l’aide des API GenerateAnswer. 

## <a name="publish-the-knowledge-base"></a>Publier la base de connaissances
Une fois que vous avez testé la base de connaissances, vous pouvez la publier. Le processus de publication envoie (push) la dernière version de la base de connaissances testée à un index de Recherche Azure dédié, qui représente la base de connaissances **publiée**. Il crée également un point de terminaison qui peut être appelé dans votre application ou bot conversationnel.

De cette manière, les modifications apportées à la version de test de la base de connaissances n’impactent pas la version publiée qui est potentiellement utilisée dans une application de production.

Chacune de ces bases de connaissances peut être ciblée séparément pour le test. À l’aide des API, vous pouvez cibler la version de test de la base de connaissances, en ajoutant l’indicateur `isTest=true` dans l’appel generateAnswer.

Découvrez comment [publier votre base de connaissances](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Surveiller l’utilisation
Pour pouvoir enregistrer les journaux de conversations du service, vous devez activer Application Insights quand vous [créez votre service QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Cela vous permet d’obtenir diverses analytiques sur l’utilisation de votre service. Découvrez comment utiliser les insights de l’application pour obtenir des [analytiques sur votre service QnA Maker](../How-To/get-analytics-knowledge-base.md).

Selon ce que vous apprenez des analytiques, effectuez les [mises à jour appropriées pour votre base de connaissances](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Score de confiance](./confidence-score.md)

## <a name="see-also"></a>Voir aussi 

[Base de connaissances](./knowledge-base.md)
[Vue d’ensemble de QnA Maker](../Overview/overview.md)
