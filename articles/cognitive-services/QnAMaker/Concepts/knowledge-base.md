---
title: Base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: Une base de connaissances QnA Maker est composée d’un ensemble de paires de questions/réponses (QnA) et de métadonnées facultatives associées à chaque paire QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955234"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Qu’est-ce qu’une base de connaissances QnA Maker ?

Une base de connaissances QnA Maker est composée d’un ensemble de paires de questions/réponses (QnA) et de métadonnées facultatives associées à chaque paire QnA.

## <a name="key-knowledge-base-concepts"></a>Principaux concepts liés aux bases de connaissances

* **Questions** : une question contient du texte qui représente une requête utilisateur. 
* **Réponses** : une réponse est la réponse retournée quand une requête de l’utilisateur est mise en correspondance avec la question associée.  
* **Métadonnées** : les métadonnées sont des étiquettes associées à une paire QnA. Elles sont représentées sous forme de paires clé-valeur. Les balises de métadonnées servent à filtrer les paires QnA et à limiter le jeu sur lequel la mise en correspondance de requête est effectuée.

Une seule entité QnA, représentée par un ID numérique de QnA, a plusieurs variantes d’une question (autres questions), toutes mappées à une réponse unique. De plus, chaque paire de ce type peut avoir plusieurs champs de métadonnées associés : une clé et une valeur.

![ID de base de connaissances QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format du contenu de la base de connaissances

Quand vous ingérez du contenu riche dans une base de connaissances, QnA Maker tente de le convertir en Markdown. Pour comprendre les formats Markdown compréhensibles par la plupart des clients de conversation, consultez [ce](https://aka.ms/qnamaker-docs-markdown-support) blog.

Les champs de métadonnées sont composés de paires clé-valeur séparées par un signe deux-points **(Product:Shredder)** . La clé et la valeur doivent être uniquement du texte. La clé de métadonnées ne doit pas contenir d’espace. Les métadonnées ne prennent en charge qu’une seule valeur par clé.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Comment QnA Maker traite une requête de l’utilisateur pour sélectionner la meilleure réponse

Le base de connaissances QnA Maker entraînée et [publiée](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) reçoit une requête de l’utilisateur, à partir d’un robot ou d’une autre application cliente, au niveau de l’[API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Le diagramme suivant illustre le processus enclenché lors de la réception de la requête de l’utilisateur.

![Processus de classement par ordre de priorité pour une requête de l’utilisateur](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Le processus est expliqué dans le tableau suivant :

|Étape|Objectif|
|--|--|
|1|L’application cliente envoie la requête de l’utilisateur vers l’[API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|Prétraitement QnA Maker de la requête de l’utilisateur avec détection de la langue, vérificateurs d’orthographe et analyseurs lexicaux.|
|3|Ce prétraitement modifie la requête de l’utilisateur afin d’offrir de meilleurs résultats de recherche.|
|4|Cette requête modifiée est envoyée à l’Index Recherche Azure et reçoit le nombre de résultats `top`. Si la réponse correcte n’est pas dans ces résultats, augmentez légèrement la valeur de `top`. En règle générale, une valeur de 10 pour `top` fonctionne dans 90 % des requêtes.|
|5\.|QnA Maker s’applique à une personnalisation avancée afin de déterminer l’exactitude des résultats Recherche Azure récupérés pour la requête de l’utilisateur. |
|6|Le modèle d’outil de classement entraîné utilise le score de fonctionnalité, de l’étape 5, pour classer les résultats Recherche Azure.|
|7|Les nouveaux résultats sont renvoyés vers l’application cliente selon l’ordre de classement.|
|||

Les fonctionnalités utilisées incluent, mais sans s’y limiter, la sémantique au niveau des mots, l’importance des termes dans un corpus et les modèles sémantiques ayant fait l’objet d’un apprentissage profond pour déterminer la similarité et la pertinence entre deux chaînes de texte.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cycle de vie du développement d’une base de connaissances](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Voir aussi

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
