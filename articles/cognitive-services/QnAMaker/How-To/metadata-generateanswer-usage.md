---
title: Métadonnées avec l’API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: Avec QnA Maker, vous pouvez ajouter des métadonnées, sous la forme de paires clé-valeur, à vos séries de questions et réponses. Ces informations peuvent être utilisées pour filtrer des résultats de requêtes d’utilisateurs et pour stocker des informations supplémentaires utilisables dans des conversations de suivi.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: tulasim
ms.openlocfilehash: 462dfb2de8608eebd5609f7044bde03991fca3ca
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958046"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Obtenir une réponse de la base de connaissances avec les API de GenerateAnswer et les métadonnées

Pour obtenir la réponse prédite à la question de l’utilisateur, utilisez l’API GenerateAnswer. Lorsque vous publiez une base de connaissances, ces informations pour utiliser cette API sont indiquées sur la page de publication. Vous pouvez également configurer l’API pour filtrer les réponses en fonction des balises de métadonnées et tester la base de connaissances à partir du point de terminaison avec le paramètre de chaîne de requête de test.

Avec QnA Maker, vous pouvez ajouter des métadonnées, sous la forme de paires clé-valeur, à vos séries de questions et réponses. Ces informations peuvent être utilisées pour filtrer des résultats de requêtes d’utilisateurs et pour stocker des informations supplémentaires utilisables dans des conversations de suivi. Pour plus d’informations, consultez [Base de connaissances](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Stocker des questions et réponses avec une entité QnA

Tout d’abord, il est important de bien comprendre de quelle façon QnA Maker stocke les données des questions et réponses. L’illustration suivante représente une entité QnA :

![Entité QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Chaque entité QnA a un ID unique et persistant. Cet ID peut être utilisé pour mettre à jour une entité QnA particulière.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obtenir des prédictions de réponse avec l’API GenerateAnswer

Utilisez l’API GenerateAnswer dans votre Bot ou application pour rechercher dans votre base de connaissances une réponse à une question d’un utilisateur et obtenir la meilleure correspondance parmi les séries de questions et réponses.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publication afin d’obtenir le point de terminaison GenerateAnswer 

Quand vous publiez votre base de connaissances à partir du [portail QnA Maker](https://www.qnamaker.ai), ou à l’aide de [l’API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), vous pouvez obtenir les détails de votre point de terminaison GenerateAnswer.

Pour obtenir les détails de votre point de terminaison :
1. Connectez-vous à [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Dans **My knowledge bases** (Mes bases de connaissances), cliquez sur **View Code** (Afficher le code) sur la ligne de votre base de connaissances.
    ![Mes bases de connaissances](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Examinez les détails de votre point de terminaison GenerateAnswer.

    ![Détails du point de terminaison](../media/qnamaker-how-to-metadata-usage/view-code.png)

Vous pouvez également obtenir les détails de votre point de terminaison à partir de l’onglet **Settings** (Paramètres) de votre base de connaissances.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuration de la demande GenerateAnswer

Vous appelez GenerateAnswer à l’aide d’une requête HTTP POST. Pour obtenir un exemple de code qui montre comment appeler GenerateAnswer, consultez les [guides de démarrage rapide](../quickstarts/csharp.md).

Le **URL de demande** a le format suivant : 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer?isTest=true
```

|Propriété de demande HTTP|Nom|Type|Objectif|
|--|--|--|--|
|Paramètre de routage d’URL|ID de la base de connaissances|string|GUID de votre base de connaissances.|
|Paramètre de routage d’URL|Hôte du point de terminaison QnAMaker|string|nom d’hôte du point de terminaison déployé dans votre abonnement Azure. Il est disponible dans la page Paramètres une fois que vous publiez la base de connaissances. |
|En-tête|Content-Type|string|type de média du corps envoyé à l’API. Valeur par défaut est : ''|
|En-tête|Authorization|string|clé de votre point de terminaison (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corps de publication|Objet JSON|JSON|La question avec des paramètres|
|Paramètre de chaîne de requête (facultatif)|`isTest`|booléenne|Si défini sur true, renvoie les résultats à partir de `testkb` index de recherche au lieu de l’index publié.|

Le corps JSON a plusieurs paramètres :

|Propriété de corps JSON|Obligatoire|Type|Objectif|
|--|--|--|--|
|`question`|required|string|Une question de l’utilisateur à envoyer à votre base de connaissances.|
|`top`|facultatif|integer|nombre de résultats classés à inclure dans la sortie. La valeur par défaut est 1.|
|`userId`|facultatif|string|ID unique d’identification de l’utilisateur. Cet ID est enregistré dans les journaux d’activité de conversations.|
|`strictFilters`|facultatif|string|si elle est spécifiée, cette chaîne indique à QnA Maker de retourner uniquement les réponses qui contiennent les métadonnées spécifiées.|

Un exemple de corps JSON ressemble à :

```json
{
    "question": "qna maker and luis",
    "top": 6,
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
|Metadata.Value : valeur des métadonnées. (chaîne, longueur maximale : 100, obligatoire)|


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

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>À l’aide de métadonnées vous permet de filtrer des réponses par balises de métadonnées personnalisées

Ajout de métadonnées vous permet de filtrer les réponses par ces balises de métadonnées. Tenez compte des informations fournies dans le forum aux questions ci-dessous. Ajoutez des métadonnées à votre base de connaissances en cliquant sur l’icône des métadonnées.

![ajouter des métadonnées](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrer les résultats avec strictFilters pour les balises de métadonnées

Prenez la question de l’utilisateur « Quand cet hôtel ferme-t-il ? » où l’intention est implicite pour le restaurant « Paradise ».

Étant donné que les résultats sont requis uniquement pour le restaurant « Paradise », vous pouvez définir un filtre dans l’appel GenerateAnswer sur les métadonnées du nom du restaurant, comme ci-dessous.

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

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Utilisez les résultats de questions et réponses pour conserver le contexte de conversation

La réponse à la GenerateAnswer contient les informations de métadonnées correspondantes du jeu de mise en correspondance de questions/réponses. Ces informations peuvent être utilisées dans votre application cliente pour stocker le contexte de la conversation précédente pour une utilisation dans des conversations plus loin. 

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

## <a name="next-steps"></a>Étapes suivantes

La page de publication fournit aussi des informations pour générer une réponse avec [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) et [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Créer une base de connaissances](./create-knowledge-base.md)
