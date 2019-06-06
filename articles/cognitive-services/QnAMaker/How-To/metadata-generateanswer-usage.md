---
title: Métadonnées avec l’API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: Avec QnA Maker, vous pouvez ajouter des métadonnées, sous la forme de paires clé-valeur, à vos séries de questions et réponses. Vous pouvez filtrer les résultats aux requêtes des utilisateurs et stocker des informations supplémentaires qui peuvent être utilisées dans les conversations de suivi.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/30/2019
ms.author: tulasim
ms.openlocfilehash: b18d47b4b09c6fa9c4d5f0ef87d7ebe73f151c60
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693232"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtenir une réponse à l’aide de l’API de GenerateAnswer et métadonnées

Pour obtenir la réponse prédite à la question de l’utilisateur, utilisez l’API GenerateAnswer. Lorsque vous publiez une base de connaissances, vous pouvez voir des informations sur l’utilisation de cette API sur le **publier** page. Vous pouvez également configurer l’API pour filtrer les réponses en fonction des balises de métadonnées et tester la base de connaissances à partir du point de terminaison avec le paramètre de chaîne de requête de test.

QnA Maker vous permet d’ajouter des métadonnées, sous la forme de paires clé / valeur, à vos jeux de questions et réponses. Vous pouvez ensuite utiliser ces informations pour filtrer les résultats aux requêtes des utilisateurs et pour stocker des informations supplémentaires qui peuvent être utilisées dans les conversations de suivi. Pour plus d’informations, consultez [Base de connaissances](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Store questions et réponses avec une entité QnA

Il est important de comprendre comment QnA Maker stocke les données en question et la réponse. L’illustration suivante représente une entité QnA :

![Illustration d’une entité QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Chaque entité QnA a un ID unique et persistant. Vous pouvez utiliser l’ID pour mettre à jour une entité QnA particulière.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obtenir des prédictions de réponse avec l’API GenerateAnswer

Vous utiliser l’API GenerateAnswer dans votre application ou un robot pour interroger votre base de connaissances avec une question de l’utilisateur, pour obtenir la meilleure correspondance à partir de la question et la réponse définit.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publication afin d’obtenir le point de terminaison GenerateAnswer 

Une fois que vous publiez votre base de connaissances à partir de la [portail QnA Maker](https://www.qnamaker.ai), ou à l’aide de la [API](https://go.microsoft.com/fwlink/?linkid=2092179), vous pouvez obtenir les détails de votre point de terminaison GenerateAnswer.

Pour obtenir les détails de votre point de terminaison :
1. Connectez-vous à [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Dans **mes bases de connaissances**, sélectionnez **afficher le Code** pour votre base de connaissances.
    ![Capture d’écran de mes bases de connaissances](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Examinez les détails de votre point de terminaison GenerateAnswer.

    ![Capture d’écran de détails de point de terminaison](../media/qnamaker-how-to-metadata-usage/view-code.png)

Vous pouvez également obtenir les détails de votre point de terminaison à partir de l’onglet **Settings** (Paramètres) de votre base de connaissances.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuration de la demande GenerateAnswer

Vous appelez GenerateAnswer à l’aide d’une requête HTTP POST. Pour obtenir un exemple de code qui montre comment appeler GenerateAnswer, consultez les [guides de démarrage rapide](../quickstarts/csharp.md).

Le **URL de demande** a le format suivant : 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Propriété de demande HTTP|Nom|Type|Objectif|
|--|--|--|--|
|Paramètre de routage d’URL|ID de la base de connaissances|string|GUID de votre base de connaissances.|
|Paramètre de routage d’URL|Hôte du point de terminaison QnAMaker|string|nom d’hôte du point de terminaison déployé dans votre abonnement Azure. Cette option est disponible sur le **paramètres** page une fois que vous publiez la base de connaissances. |
|En-tête|Content-Type|string|type de média du corps envoyé à l’API. Valeur par défaut est : ''|
|En-tête|Authorization|string|clé de votre point de terminaison (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corps de publication|Objet JSON|JSON|La question avec les paramètres.|


Le corps JSON a plusieurs paramètres :

|Propriété de corps JSON|Obligatoire|Type|Objectif|
|--|--|--|--|
|`question`|required|string|Une question de l’utilisateur à envoyer à votre base de connaissances.|
|`top`|facultatif|integer|nombre de résultats classés à inclure dans la sortie. La valeur par défaut est 1.|
|`userId`|facultatif|string|ID unique d’identification de l’utilisateur. Cet ID est enregistré dans les journaux d’activité de conversations.|
|`scoreThreshold`|facultatif|integer|Seront affichera uniquement les réponses avec le score de confiance au-dessus de ce seuil. La valeur par défaut est 0.|
|`isTest`|facultatif|Boolean|Si défini sur true, renvoie les résultats à partir de `testkb` index de recherche au lieu de l’index publié.|
|`strictFilters`|facultatif|string|si elle est spécifiée, cette chaîne indique à QnA Maker de retourner uniquement les réponses qui contiennent les métadonnées spécifiées. Utilisez `none` pour indiquer la réponse ne doit comporter aucun filtre de métadonnées. |
|`RankerType`|facultatif|string|S’il est spécifié en tant que `QuestionOnly`, indique à QnA Maker pour rechercher uniquement les questions. Si non spécifié, QnA Maker recherche questions et réponses.

Un exemple de corps JSON ressemble à :

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

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriétés de la réponse GenerateAnswer

Une réponse réussie retourne un état de 200 et une réponse JSON. 

|Propriété des réponses (classée par score)|Objectif|
|--|--|
|de votre application|score de classement compris entre 0 et 100.|
|ID|ID unique assigné à la réponse.|
|questions|questions fournies par l’utilisateur.|
|répondre|réponse à la question.|
|source|nom de la source à partir de laquelle la réponse a été extraite ou enregistrée dans la base de connaissances.|
|metadata|métadonnées associées à la réponse.|
|metadata.name|nom des métadonnées. (chaîne, longueur maximale : 100, obligatoire)|
|metadata.value|valeur des métadonnées. (chaîne, longueur maximale : 100, obligatoire)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Utiliser des métadonnées pour filtrer les réponses par balises de métadonnées personnalisées

Ajout de métadonnées vous permet de filtrer les réponses par ces balises de métadonnées. Ajouter la colonne de métadonnées à partir de la **Options d’affichage** menu. Ajouter des métadonnées à votre base de connaissances en sélectionnant les métadonnées **+** icône pour ajouter une paire de métadonnées. Cette paire se compose d’une clé et une valeur.

![Capture d’écran de l’ajout de métadonnées](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrer les résultats avec strictFilters pour les balises de métadonnées

Envisagez la question de l’utilisateur « Lorsque does fermer cet hôtel ? », où l’intention est implicite pour le restaurant « Paradise. »

Étant donné que les résultats sont requis uniquement pour le restaurant « Paradise », vous pouvez définir un filtre dans l’appel de GenerateAnswer sur les métadonnées « Restaurant Name ». L’exemple suivant illustre cela :

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Utilisez les résultats de questions et réponses pour conserver le contexte de conversation

La réponse à la GenerateAnswer contient les informations de métadonnées correspondantes de l’ensemble de questions et réponses mise en correspondance. Vous pouvez utiliser ces informations dans votre application cliente pour stocker le contexte de la conversation précédente pour une utilisation dans des conversations plus loin. 

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

## <a name="match-questions-only-by-text"></a>Correspondance de questions, en texte

Par défaut, QnA Maker effectue une recherche dans les questions et réponses. Si vous souhaitez effectuer une recherche dans les questions uniquement, pour générer une réponse, utilisez le `RankerType=QuestionOnly` dans le corps POST de la demande GenerateAnswer.

Vous pouvez parcourir la base de connaissances publié, à l’aide de `isTest=false`, ou dans la base de connaissances de test à l’aide de `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Étapes suivantes

Le **publier** page fournit également des informations pour générer une réponse avec [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) et [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Créer une base de connaissances](./create-knowledge-base.md)
