---
title: Filtrer en fonction du contexte à l’aide de métadonnées
titleSuffix: Azure Cognitive Services
description: QnA Maker filtre les paires QnA par métadonnées.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017421"
---
# <a name="filter-responses-with-metadata"></a>Filtrer les réponses à l’aide de métadonnées

Avec QnA Maker, vous pouvez ajouter des métadonnées, sous la forme de paires clé-valeur, à vos paires question/réponse. Vous pouvez ensuite utilise ces informations pour filtrer des résultats de requêtes d’utilisateurs et pour stocker des informations supplémentaires utilisables dans des conversations de suivi.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Stocker des questions et réponses avec une entité QnA

Il est important de bien comprendre de quelle façon QnA Maker stocke les données des questions et réponses. L’illustration suivante représente une entité QnA :

![Illustration d’une entité QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Chaque entité QnA a un ID unique et persistant. Vous pouvez utiliser l’ID pour mettre à jour une entité QnA particulière.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Utiliser des métadonnées pour filtrer les réponses sur des balises de métadonnées personnalisées

L’ajout de métadonnées permet de filtrer les réponses sur ces balises de métadonnées. Ajoutez la colonne de métadonnées à partir du menu **Options d’affichage**. Ajoutez des métadonnées à votre Vase de connaissances en sélectionnant l’icône **+** des métadonnées pour ajouter une paire de métadonnées. Cette paire se compose d’une clé et une valeur.

![Capture d’écran de l’ajout de métadonnées](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrer les résultats avec strictFilters pour les balises de métadonnées

Imaginez que la question de l’utilisateur soit « Quand ferme cet hôtel ? », où l’intention a implicitement trait au restaurant « Paradise ».

Étant donné que les résultats sont requis uniquement pour le restaurant « Paradise », vous pouvez définir un filtre dans l’appel GenerateAnswer sur les métadonnées du nom du restaurant. L’exemple suivant illustre cela :

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>AND logique par défaut

Pour combiner plusieurs filtres de métadonnées dans la requête, ajoutez les filtres de métadonnées supplémentaires au tableau de la propriété `strictFilters`. Par défaut, les valeurs sont combinées logiquement (AND). Une combinaison logique exige que tous les filtres correspondent aux paires de question/réponse pour que la paire soit retournée dans la réponse.

Cela équivaut à utiliser la propriété `strictFiltersCompoundOperationType` avec la valeur `AND`.

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>OR logique en utilisant la propriété strictFiltersCompoundOperationType

Quand plusieurs filtres de métadonnées sont combinés, si vous êtes seulement intéressé par la correspondance d’un ou quelques filtres, utilisez la propriété `strictFiltersCompoundOperationType` avec la valeur `OR`.

Votre base de connaissances retourne alors des réponses quand un filtre correspond, mais ne retourne pas de réponses dépourvues de métadonnées.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Exemples de métadonnées dans les guides de démarrage rapide

Apprenez-en plus sur les métadonnées dans le guide de démarrage rapide du portail QnA Maker pour les métadonnées :
* [Création – Ajouter des métadonnées à une paire QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Prédiction de requête – Filtrer les réponses par métadonnées](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Utilisez les résultats de questions et réponses pour conserver le contexte de la conversation

La réponse à GenerateAnswer contient les informations de métadonnées correspondantes de la paire question/réponse trouvée. Vous pouvez utiliser ces informations pour stocker le contexte de la conversation précédente en vue d’une utilisation dans des conversations ultérieures.

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

> [!div class="nextstepaction"]
> [Analyser votre base de connaissances](../How-to/get-analytics-knowledge-base.md)
