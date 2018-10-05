---
title: Métadonnées avec l’API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: Avec QnA Maker, vous pouvez ajouter des métadonnées, sous la forme de paires clé-valeur, à vos séries de questions et réponses. Ces informations peuvent être utilisées pour filtrer des résultats de requêtes d’utilisateurs et pour stocker des informations supplémentaires utilisables dans des conversations de suivi.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim88
ms.openlocfilehash: eef26cf1f5a11d7dcd1fdc41747aac675e0bc528
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031082"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Utiliser des métadonnées et l’API GenerateAnswer

Avec QnA Maker, vous pouvez ajouter des métadonnées, sous la forme de paires clé-valeur, à vos séries de questions et réponses. Ces informations peuvent être utilisées pour filtrer des résultats de requêtes d’utilisateurs et pour stocker des informations supplémentaires utilisables dans des conversations de suivi. Pour plus d’informations, consultez [Base de connaissances](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Entité QnA

Tout d’abord, il est important de bien comprendre de quelle façon QnA Maker stocke les données des questions et réponses. L’illustration suivante représente une entité QnA :

![Entité QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Chaque entité QnA a un ID unique et persistant. Cet ID peut être utilisé pour mettre à jour une entité QnA particulière.

## <a name="generateanswer-api"></a>API GenerateAnswer

Utilisez l’API GenerateAnswer dans votre Bot ou application pour rechercher dans votre base de connaissances une réponse à une question d’un utilisateur et obtenir la meilleure correspondance parmi les séries de questions et réponses.

### <a name="generateanswer-endpoint"></a>Point de terminaison GenerateAnswer

Quand vous publiez votre base de connaissances à partir du [portail QnA Maker](https://www.qnamaker.ai), ou à l’aide de [l’API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), vous pouvez obtenir les détails de votre point de terminaison GenerateAnswer.

Pour obtenir les détails de votre point de terminaison :
1. Connectez-vous à [https://www.qnamaker.ai](https://www.qnamaker.ai).
2. Dans **My knowledge bases** (Mes bases de connaissances), cliquez sur **View Code** (Afficher le code) sur la ligne de votre base de connaissances.
![Mes bases de connaissances](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Examinez les détails de votre point de terminaison GenerateAnswer.

![Détails du point de terminaison](../media/qnamaker-how-to-metadata-usage/view-code.png)

Vous pouvez également obtenir les détails de votre point de terminaison à partir de l’onglet **Settings** (Paramètres) de votre base de connaissances.

### <a name="generateanswer-request"></a>Requête GenerateAnswer

Vous appelez GenerateAnswer à l’aide d’une requête HTTP POST. Pour obtenir un exemple de code qui montre comment appeler GenerateAnswer, consultez les [guides de démarrage rapide](../quickstarts/csharp.md).

- **URL de la requête** : https://{point de terminaison QnA Maker}/knowledgebases/{ID de la base de connaissances}/generateAnswer

- **Paramètres de la requête** : 
    - **Knowledge base ID** (chaîne) : GUID de votre base de connaissances.
    - **QnAMaker endpoint** (chaîne) : nom d’hôte du point de terminaison déployé dans votre abonnement Azure.
- **En-têtes de requête**
    - **Content-Type** (chaîne) : type de média du corps envoyé à l’API.
    - **Autorisation** (chaîne) : votre clé de point de terminaison (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Corps de la demande**
    - **question** (chaîne) : question de l’utilisateur qui fait l’objet d’une recherche dans votre base de connaissances.
    - **top** (entier facultatif) : nombre de résultats classés à inclure dans la sortie. La valeur par défaut est 1.
    - **userId** (chaîne facultative) : identificateur unique de l’utilisateur. Cet ID est enregistré dans les journaux de conversations.
    - **strictFilters** (chaîne facultative) : cette chaîne, le cas échéant, indique à QnA Maker de retourner uniquement les réponses qui contiennent les métadonnées spécifiées. Pour plus d’informations, voir plus bas.
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

### <a name="generateanswer-response"></a>Réponse GenerateAnswer

- **Réponse 200** : appel réussi qui retourne le résultat de la question. La réponse contient les champs suivants :
    - **answers** : liste de réponses à la requête utilisateur, triées par ordre décroissant du score de classement.
        - **score** : score de classement compris entre 0 et 100.
        - **questions** : questions posées par l’utilisateur.
        - **réponse** : réponse à la question.
        - **source** : nom de la source à partir de laquelle la réponse a été extraite ou enregistrée dans la base de connaissances.
        - **metadata** : métadonnées associées à la réponse.
            - name : nom des métadonnées. (chaîne obligatoire, longueur maximale : 100)
            - value : valeur des métadonnées. (chaîne obligatoire, longueur maximale : 100)
        - **Id** : ID unique assigné à la réponse.
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

## <a name="metadata-example"></a>Exemple de métadonnées

Examinez les données FAQ ci-dessous, relatives aux restaurants situés à Hyderabad. Ajoutez des métadonnées à votre base de connaissances en cliquant sur l’icône d’engrenage.

![ajouter des métadonnées](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Filtrer les résultats avec strictFilters

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

### <a name="keep-context"></a>Conserver le contexte
La réponse à GenerateAnswer contient les informations de métadonnées correspondantes de la série des questions/réponses avec correspondance, comme suit.

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

Ces informations peuvent être utilisées pour enregistrer le contexte de la conversation précédente en vue d’une utilisation dans les conversations ultérieures. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une base de connaissances](./create-knowledge-base.md)
