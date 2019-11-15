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
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 355556e98300ecad6aa3141f0f4ab14b834cd91e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794896"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Qu’est-ce qu’une base de connaissances QnA Maker ?

Une base de connaissances QnA Maker est composée d’un ensemble de paires de questions/réponses (QnA) et de métadonnées facultatives associées à chaque paire QnA.

## <a name="key-knowledge-base-concepts"></a>Principaux concepts liés aux bases de connaissances

* **Questions** : une question contient du texte qui représente une requête utilisateur. 
* **Réponses** : une réponse est ce qui est retourné quand une requête utilisateur correspond à la question associée.  
* **Métadonnées** : les métadonnées sont des étiquettes associées à une paire QnA. Elles sont représentées sous forme de paires clé-valeur. Les balises de métadonnées servent à filtrer les paires QnA et à limiter le jeu sur lequel la mise en correspondance de requête est effectuée.

Une seule entité QnA, représentée par un ID numérique de QnA, a plusieurs variantes d’une question (autres questions), toutes mappées à une réponse unique. De plus, chaque paire de ce type peut avoir plusieurs champs de métadonnées associés : une clé et une valeur.

![ID de base de connaissances QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format du contenu de la base de connaissances

Quand vous ingérez du contenu riche dans une base de connaissances, QnA Maker tente de le convertir en Markdown. Pour comprendre les formats Markdown compréhensibles par la plupart des clients de conversation, lisez [ce blog](https://aka.ms/qnamaker-docs-markdown-support).

Les champs de métadonnées sont composés de paires clé-valeur séparées par un signe deux-points, comme Product:Shredder. La clé et la valeur doivent être uniquement du texte. La clé de métadonnées ne doit pas contenir d’espace. Les métadonnées ne prennent en charge qu’une seule valeur par clé.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Comment QnA Maker traite une requête de l’utilisateur pour sélectionner la meilleure réponse

Le base de connaissances QnA Maker entraînée et [publiée](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) reçoit une requête de l’utilisateur, à partir d’un robot ou d’une autre application cliente, au niveau de l’[API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Le diagramme suivant illustre le processus enclenché lors de la réception de la requête de l’utilisateur.

![Processus de classement par ordre de priorité pour une requête de l’utilisateur](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processus de classement

Le processus est expliqué dans le tableau suivant.

|Étape|Objectif|
|--|--|
|1|L’application cliente envoie la requête de l’utilisateur vers l’[API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker prétraite la requête de l’utilisateur avec détection de la langue, vérificateurs d’orthographe et analyseurs lexicaux.|
|3|Ce prétraitement modifie la requête utilisateur afin d’offrir des résultats de recherche optimaux.|
|4|Cette requête modifiée est envoyée à l’Index de Recherche cognitive Azure qui reçoit `top` résultats. Si la réponse correcte n’est pas dans ces résultats, augmentez légèrement la valeur de `top`. En règle générale, une valeur de 10 pour `top` fonctionne dans 90 % des requêtes.|
|5\.|QnA Maker s’applique à une personnalisation avancée afin de déterminer l’exactitude des résultats récupérés pour la requête utilisateur. |
|6|Le modèle d’outil de classement formé utilise le score de fonctionnalité, de l’étape 5, pour classer les résultats Recherche cognitive Azure.|
|7|Les nouveaux résultats sont renvoyés vers l’application cliente selon l’ordre de classement.|
|||

Les fonctionnalités utilisées incluent notamment la sémantique au niveau des mots, l’importance des termes dans un corpus et les modèles sémantiques ayant fait l’objet d’un apprentissage profond pour déterminer la similarité et la pertinence entre deux chaînes de texte.

## <a name="http-request-and-response-with-endpoint"></a>Requête et réponse HTTP avec point de terminaison
Lorsque vous publiez votre base de connaissance, le service crée un point de terminaison HTTP basé sur REST pouvant être intégré dans votre application, généralement un bot conversationnel. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Demande de requête de l’utilisateur pour générer une réponse

Une requête utilisateur est la question que l’utilisateur final pose à la base de connaissances, telle que `How do I add a collaborator to my app?`. Cette requête est souvent dans un format de langage naturel ou composée de quelques mots clés qui représentent la question, tels que `help with collaborators`. La requête est envoyée à votre base de connaissances à partir d’une requête HTTP dans votre application cliente.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Vous contrôlez la réponse en définissant des propriétés telles que[scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers), et [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Utilisez [le contenu de la conversation](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) avec [la fonctionnalité multitour](../how-to/multiturn-conversation.md) pour que la conversation se poursuive afin d’affiner les questions et les réponses pour trouver la réponse correcte et définitive.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Réponse d’un appel pour générer une réponse

La réponse HTTP est la réponse extraite de la base de données. Il s’agit de la meilleure correspondance à une requête utilisateur donnée. La réponse comprend la solution et le score de prédiction. Si vous avez demandé plusieurs réponses principales, avec la propriété `top`, vous obtenez plus d’une réponse principale, chacune avec un score. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>Base de connaissances de test et de production
Une base de connaissances est le référentiel de questions et réponses créées, conservées et utilisées par le biais de QnA Maker. Chaque niveau QnA Maker peut servir pour plusieurs bases de connaissances.

Une base de connaissances a deux états : *test* et *publiée*.

La *base de connaissances de test* est la version en cours de modification, d’enregistrement et de test en lien avec la précision et l’exhaustivité des réponses. Les modifications apportées à la base de connaissances de test n’affectent pas l’utilisateur final de votre application ou bot conversationnel. La base de connaissances de test est connue comme `test` dans la requête HTTP. 

La *base de connaissances publiée* est la version utilisée dans votre bot conversationnel ou application. La publication d’une base de connaissances a pour effet de placer le contenu de la base de connaissances de test dans la base de connaissance publiée. Étant donné que la base de connaissances publiée est la version que l’application utilise via le point de terminaison, assurez-vous que le contenu est correct et dûment testé. La base de connaissances de test est connue comme `prod` dans la requête HTTP.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cycle de vie du développement d’une base de connaissances](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Voir aussi

[Vue d’ensemble de QnA Maker](../Overview/overview.md)

Créez et modifiez la base de connaissances avec : 
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Générez une réponse avec : 
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
