---
title: Guide pratique pour tester une base de connaissances - QnA Maker
titlesuffix: Azure Cognitive Services
description: Le test de votre base de connaissances QnA Maker est une partie importante d’un processus itératif afin d’améliorer la précision des réponses retournées. Vous pouvez tester la base de connaissances via une interface de conversation améliorée qui vous permet également d’apporter des modifications.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/17/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 20ebcb502e03f2d817fe18624d8c790e920c667f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61371885"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Tester votre base de connaissances de manière interactive dans QnA Maker

Le test de votre base de connaissances QnA Maker est une partie importante d’un processus itératif afin d’améliorer la précision des réponses retournées. Vous pouvez tester la base de connaissances via une interface de conversation améliorée qui vous permet également d’apporter des modifications.

## <a name="test-answer-matching"></a>Tester la concordance de réponse

1.  Accédez à votre base de connaissances en sélectionnant son nom dans la page **Mes bases de connaissances**.
2.  Pour accéder au panneau déroulant Test, sélectionnez **Test** dans le panneau supérieur de votre application.

    ![Accéder au panneau Test](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Entrez une question dans la zone de texte, puis sélectionnez Entrée.

4.  La réponse la plus concordante de la base de connaissances est retournée.

## <a name="clear-test-panel"></a>Effacer le panneau Test

Pour effacer toutes les requêtes de test entrées et leurs résultats de la console de test, sélectionnez **Recommencer** dans l’angle supérieur gauche du panneau Test.

## <a name="close-test-panel"></a>Fermer le panneau Test

Pour fermer le panneau Test, sélectionnez à nouveau le bouton  **Test** . Tant que le panneau Test est ouvert, vous ne pouvez pas modifier le contenu de la base de connaissances.

## <a name="inspect-score"></a>Inspecter le score

Vous pouvez inspecter les détails du résultat du test dans le panneau Inspecter.

1.  Dans le panneau déroulant Test ouvert, sélectionnez **Inspecter** pour obtenir des détails sur la réponse.

    ![Inspecter des réponses](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Le panneau Inspection s’affiche. Le panneau inclut l’intention de score la plus élevée, ainsi que toutes les entités identifiées. Le panneau affiche le résultat de l’énoncé sélectionné.

## <a name="correct-the-top-scoring-answer"></a>Corriger la réponse avec le score le plus élevé

Si la réponse avec le score le plus élevé est incorrecte, sélectionnez la réponse correcte dans la liste et sélectionnez **Enregistrer et former**.

![Corriger la réponse avec le score le plus élevé](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Vous pouvez ajouter des formes alternatives d’une question à une réponse donnée. Tapez les réponses alternatives dans la zone de texte et cliquez sur Entrée pour les ajouter. Sélectionnez **Enregistrer et former** pour stocker les mises à jour.

![Ajouter des questions alternatives](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Ajouter une nouvelle réponse

Vous pouvez ajouter une nouvelle réponse si les réponses existantes qui ont été mises en correspondance sont incorrectes ou si la réponse n’existe pas dans la base de connaissances (aucune bonne correspondance trouvée dans la base de connaissances). Entrez la nouvelle réponse à la question actuelle dans la zone de texte et appuyez sur Entrée pour l’ajouter. 

Sélectionnez **Enregistrer et former** pour conserver cette réponse. Une nouvelle paire question/réponse a maintenant été ajoutée à votre base de connaissances.

![Ajouter une nouvelle paire question/réponse](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Toutes les modifications apportées à votre base de connaissances ne sont enregistrées que lorsque vous appuyez sur le bouton **Enregistrer et former**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Publier une base de connaissances](./publish-knowledge-base.md)
