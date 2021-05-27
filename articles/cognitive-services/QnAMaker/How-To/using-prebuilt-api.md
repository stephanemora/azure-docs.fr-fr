---
title: API prédéfinie QnA Maker
titleSuffix: Azure Cognitive Services
description: Utiliser l’API prédéfinie pour obtenir des réponses sur un texte
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/05/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0aa26cc0d78d6ca20dccb97b3936bad840c989b7
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382544"
---
# <a name="prebuilt-question-answering"></a>Réponses aux questions prédéfinies

La fonctionnalité Réponses aux questions prédéfinies permet de répondre aux questions d’un utilisateur sur un passage de texte sans devoir créer de base de connaissances, de conserver les paires questions-réponse, ou d’occasionner un coût pour une infrastructure sous-exploitée. Cette fonctionnalité fournie en tant qu’API peut être utilisée pour satisfaire des besoins en matière de questions et de réponses sans avoir à apprendre les détails de QnA Maker ou d’un stockage supplémentaire.

À partir d’une requête utilisateur et d’un bloc ou passage de texte, l’API retourne une réponse précise (si disponible). 

<a name="qna-entity"></a>


## <a name="example-usage-of-prebuilt-question-answering"></a>Exemple d’utilisation de la fonctionnalité Réponses aux questions prédéfinies

Imaginez que vous disposez d’un ou plusieurs blocs de texte dans lesquels vous souhaitez trouver des réponses à une question donnée. Normalement, vous auriez dû créer autant de sources qu’il y a de blocs de texte. En revanche, désormais, la fonctionnalité Réponses aux questions prédéfinies vous permet d’interroger les blocs de texte sans avoir à définir des sources de contenu dans une base de connaissances. 

Voici d’autres scénarios dans lesquels vous pouvez utiliser l’API prédéfinie :

* Application de lecteur de livre électronique permettant aux utilisateurs de mettre du texte en surbrillance, d’entrer une question et d’obtenir des réponses en relation avec le texte sélectionné. 
* Extension de navigateur permettant aux utilisateurs de poser une question sur le contenu de la page affichée dans le navigateur.
* Robot dédié à la santé qui accepte des requêtes d’utilisateurs et fournit des réponses basées sur le contenu médical que le bot identifie comme le plus pertinent. 

Voici un exemple de demande :

## <a name="sample-request"></a>Exemple de demande
```
POST https://{Endpoint}/qnamaker/v5.0-preview.2/generateanswer
```

### <a name="sample-query-over-a-single-block-of-text"></a>Exemple de requête sur un seul bloc de texte

Corps de la demande

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "documents": [
        {
            "text": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "id": "doc1"
        }
    ],
    "Language": "en"
}
```
## <a name="sample-response"></a>Exemple de réponse

Dans le corps de la demande ci-dessus, nous effectuons une requête sur un bloc de texte. Un exemple de réponse reçue à cette requête figure ci-dessous.

```json
{
    "answers": [
        {
            "answer": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "answerSpan": {
                "text": "two to four hours",
                "score": 0.0,
                "startIndex": 47,
                "endIndex": 64
            },
            "score": 0.9599020481109619,
            "id": "doc1",
            "answerStartIndex": 0,
            "answerEndIndex": 390
        },
        {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.06749606877565384,
            "id": "doc1",
            "answerStartIndex": 129,
            "answerEndIndex": 390
        },
        {
            "answer": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.011389964260160923,
            "id": "doc1",
            "answerStartIndex": 265,
            "answerEndIndex": 390
        }
    ]
}
```
Nous voyons que l’API propose plusieurs réponses. Chaque réponse est associée à un score de confiance spécifique qui permet d’en comprendre la pertinence. L’utilisateur peut utiliser ce score de confiance pour afficher les réponses à la requête.

## <a name="prebuilt-api-limits"></a>Limites de l’API prédéfinie 

Consultez la documentation sur les [Limites d’une API prédéfinie](https://docs.microsoft.com/azure/cognitive-services/qnamaker/limits?#prebuilt-question-answering-limits) 

## <a name="prebuilt-api-reference"></a>Référence de l’API prédéfinie
Pour comprendre les paramètres d’entrée et de sortie requis pour appeler l’API, consultez la documentation de [Référence de l’API prédéfinie](https://docs.microsoft.com/rest/api/cognitiveservices-qnamaker/qnamaker5.0preview2/prebuilt/generateanswer).
