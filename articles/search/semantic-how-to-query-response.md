---
title: Structurer une réponse sémantique
titleSuffix: Azure Cognitive Search
description: Décrit l’algorithme de classement sémantique dans la Recherche cognitive, ainsi que la manière de structurer des « réponses sémantiques » et des « légendes sémantiques » à partir d’un jeu de résultats.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678532"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Classement et réponses sémantiques dans la Recherche cognitive Azure

> [!IMPORTANT]
> L’algorithme de classement sémantique et les réponses/légendes sémantiques sont en préversion publique, disponibles uniquement par le biais de l’API REST en préversion. Les fonctionnalités d’évaluation sont proposées telles quelles, dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le classement sémantique améliore la précision des résultats de la recherche en reclassant les premières correspondances à l’aide d’un modèle de classement sémantique entraîné pour les requêtes exprimées en langage naturel plutôt qu’en mots clés.

Cet article décrit l’algorithme de classement sémantique et la façon dont une réponse sémantique est mise en forme. Une réponse inclut des sous-titres, à la fois en texte brut et avec des mises en surbrillance et des réponses (facultatif).

+ Les sous-titres sémantiques sont des passages de texte relatifs à la requête extraite des résultats de recherche. Ils peuvent vous aider à synthétiser un résultat lorsque les champs de contenu individuels sont trop volumineux pour la page de résultats. Les sous-titres sont des points clés de la sémantique, qui permettent aux utilisateurs de parcourir rapidement les résultats pour trouver les documents les plus pertinents, ce qui améliore l’expérience globale de l’utilisateur.

+ Les réponses sémantiques utilisent les modèles Machine Learning de Bing pour formuler des réponses à des requêtes qui ressemblent à des questions. Les réponses sont sélectionnées dans la liste des passages les plus pertinents pour la requête, comme extraites des premiers documents du jeu de résultats de la requête. Les réponses sont retournées sous la forme d’un objet indépendant de niveau supérieur dans la charge utile de réponse à la requête que vous pouvez choisir de rendre sur les pages de recherche, ainsi que les résultats de la recherche.

## <a name="prerequisites"></a>Prérequis

+ Requêtes formulées à l’aide du type de requête sémantique. Pour plus d’informations, consultez [Créer une requête sémantique](semantic-how-to-query-request.md).

## <a name="understanding-a-semantic-response"></a>Fonctionnement d’une réponse sémantique

Une réponse sémantique comprend de nouvelles propriétés pour les scores, les légendes et les réponses. Une réponse sémantique est générée à partir de la réponse standard, en utilisant les 50 premiers résultats retournés par le [moteur de recherche en texte intégral](search-lucene-query-architecture.md), qui sont ensuite reclassés au moyen du ranker sémantique. Si plus de 50 documents sont indiqués, les résultats supplémentaires sont retournés, mais ils ne sont pas reclassés sémantiquement.

Comme pour toutes les requêtes, une réponse se compose de tous les champs marqués comme récupérables, ou seulement des champs listés dans l’instruction de sélection. Elle comprend également un champ « réponse » et des « légendes ».

+ Pour chaque résultat sémantique, par défaut, une « réponse » est retournée sous la forme d’un champ distinct, que vous pouvez choisir d’afficher dans une page de recherche. Vous pouvez en spécifier au moins cinq. La formulation de la réponse est automatisée : lecture de tous les documents dans les résultats initiaux, exécution d’une synthèse d’extraction, suivie par la compréhension de la lecture des machines, et enfin promotion d’une réponse directe à la question de l’utilisateur dans le champ de réponse.

+ Une « légende » constitue une synthèse par extraction d’un contenu de document, retournée en texte brut ou avec des mises en surbrillance. Les légendes sont incluses automatiquement et ne peuvent pas être supprimées. Les mises en surbrillance sont appliquées à l’aide de la compréhension de la lecture des machines pour identifier les chaînes devant être mises en évidence. Les mises en surbrillance attirent l’attention sur les passages les plus pertinents, afin que vous puissiez rapidement analyser une page de résultats et trouver le bon document.

Un jeu de résultats reclassé sémantiquement trie les résultats selon la valeur @search.rerankerScore. Si vous ajoutez une clause OrderBy, une erreur HTTP 400 est retournée, car cette clause n’est pas prise en charge dans une requête sémantique.

## <a name="example"></a>Exemple

@search.rerankerScore existe parallèlement à @search.score standard, et sert à reclasser les résultats.

Étant donné la requête suivante :

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Attendez-vous à voir le résultat suivant, qui est représentatif d’une réponse sémantique. Ces résultats n’affichent que les trois premières réponses, classées par @search.rerankerScore. Remarquez comment Oceanside Resort se retrouve désormais classé en premier. Sous le classement par défaut de @search.score, ce résultat serait retourné en deuxième position, après Trails End.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Étapes suivantes

+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)
+ [Algorithme de similarité](index-ranking-similarity.md)
+ [Créer une requête sémantique](semantic-how-to-query-request.md)
+ [Créer une requête de base](search-query-create.md)