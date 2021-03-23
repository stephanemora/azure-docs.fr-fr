---
title: Retourner une réponse sémantique
titleSuffix: Azure Cognitive Search
description: Décrit la composition d’une réponse sémantique et l’obtention de réponses à partir d’un jeu de résultats.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b99cbf91d7fc1c5d90753dfa1461a58eda055180
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418893"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Retourner une réponse sémantique dans Recherche cognitive Azure

> [!IMPORTANT]
> Les fonctionnalités de la recherche sémantique sont disponibles en préversion publique, uniquement par le biais de l’API REST en préversion. Les fonctionnalités en préversion sont proposées telles quelles, sous des [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), et ne sont pas garanties d’avoir la même implémentation lors de la mise à la disposition générale. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

Lors de la formulation d’une [requête sémantique](semantic-how-to-query-request.md), vous pouvez éventuellement extraire le contenu des documents de correspondance supérieure qui « répondent » directement à la requête. Une ou plusieurs réponses peuvent être incluses, et vous pouvez ensuite les afficher sur une page de recherche pour améliorer l’expérience utilisateur de votre application.

Dans cet article, vous allez apprendre à demander une réponse sémantique, à dépaqueter la réponse et découvrir les caractéristiques de contenu les plus propices à la production de réponses de haute qualité.

## <a name="prerequisites"></a>Prérequis

Toutes les conditions préalables qui s’appliquent aux [requêtes sémantiques](semantic-how-to-query-request.md) s’appliquent également aux réponses, y compris le niveau de service et la région.

+ Les requêtes formulées à l’aide de paramètres de requête sémantique et incluant le paramètre « answers ». Les paramètres requis sont décrits dans cet article.

+ Les chaînes de requête doivent être formulées dans un langage ayant les caractéristiques d’une question (quoi, où, quand, comment).

+ Les documents de recherche doivent contenir du texte ayant les caractéristiques d’une réponse, et ce texte doit exister dans un des champs indiqués dans « searchFields ».

## <a name="what-is-a-semantic-answer"></a>Qu’est-ce qu’une réponse sémantique ?

Une réponse sémantique est un artefact d’une [requête sémantique](semantic-how-to-query-request.md). Elle se compose d’un ou de plusieurs passages textuels d’un document de recherche, formulés comme une réponse à une requête qui ressemble à une question. Pour qu’une réponse soit renvoyée, des expressions ou des phrases doivent exister dans un document de recherche ayant les caractéristiques de langage d’une réponse, et la requête elle-même doit être posée comme une question.

La Recherche cognitive utilise un modèle de compréhension de lecture automatique pour formuler des réponses. Le modèle produit un ensemble de réponses potentielles à partir des documents disponibles, et lorsqu’il atteint un niveau de confiance suffisamment élevé, il propose une réponse.

Les réponses sont retournées sous la forme d’un objet indépendant de niveau supérieur dans la charge utile de réponse à la requête que vous pouvez choisir de rendre sur les pages de recherche, ainsi que les résultats de la recherche. Structurellement, il s’agit d’un élément de tableau d’une réponse qui comprend du texte, une clé de document et un score de confiance.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Comment demander des réponses sémantiques dans une requête

Pour renvoyer une réponse sémantique, la requête doit avoir le type de requête sémantique, une langue, des champs de recherche et le paramètre « answers ». La spécification du paramètre « answers » ne garantit pas que vous obtiendrez une réponse, mais la requête doit inclure ce paramètre pour que le traitement de la réponse soit appelé.

Le paramètre « searchFields » est essentiel pour renvoyer une réponse de haute qualité, à la fois en matière de contenu et d’ordre. 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Une chaîne de requête ne doit pas être null et doit être formulée comme question. Dans cette préversion, les constantes « queryType » et « queryLanguage » doivent être définies exactement comme dans l’exemple.

+ Le paramètre « searchFields » détermine les champs qui fournissent des jetons au modèle d’extraction. Veillez à définir ce paramètre. Vous devez avoir au moins un champ de chaîne, mais ajoutez tout champ de chaîne que vous pensez être utile pour fournir une réponse. Seuls environ 8 000 jetons par document sont passés dans le modèle. Démarrez la liste de champs avec des champs concis, puis progressez vers des champs de texte enrichis. Pour obtenir des conseils précis sur la façon de définir ce champ, consultez [Définir searchFields](semantic-how-to-query-request.md#searchfields).

+ Pour « answers », la construction de paramètre de base est `"answers": "extractive"`, où le nombre de réponses par défaut retourné est un. Vous pouvez augmenter le nombre de réponses en ajoutant un nombre, jusqu’à un maximum de cinq.  Le fait que vous ayez besoin de plusieurs réponses dépend de l’expérience utilisateur de votre application et de la façon dont vous souhaitez afficher les résultats.

## <a name="deconstruct-an-answer-from-the-response"></a>Décomposer une réponse à partir du résultat reçu

Les réponses sont fournies dans le tableau @search.answers, qui apparaît en premier dans le résultat reçu. Si une réponse est indéterminée, la réponse s’affiche sous la forme `"@search.answers": []`. Quand vous concevez une page de résultats de recherche qui comprend des réponses, veillez à gérer les cas où les réponses sont introuvables.

Dans @search.answers, la « clé » est la clé de document ou l’ID de la correspondance. À partir d’une clé de document, vous pouvez utiliser l’API de [Recherche de document](/rest/api/searchservice/lookup-document) pour récupérer tout ou partie du document de recherche à inclure dans la page de recherche ou une page de détails.

« text » et « highlights » fournissent des contenus identiques, en texte brut et avec des mises en évidence. Par défaut, les mises en évidence ont le style `<em>`, que vous pouvez remplacer à l’aide des paramètres highlightPreTag et highlightPostTag existants. Comme indiqué ailleurs, la substance d’une réponse est le contenu textuel d’un document de recherche. Le modèle d’extraction recherche les caractéristiques d’une réponse pour trouver le contenu approprié, mais ne compose pas de nouveau langage dans la réponse.

Le « score » est un score de confiance qui reflète la force de la réponse. S’il existe plusieurs réponses dans le résultat renvoyé, ce score est utilisé pour déterminer l’ordre. Les meilleures réponses et les principales légendes peuvent être dérivées de différents documents de recherche, où la réponse la plus haute provient d’un document et la plus haute légende d’un autre, mais en général, vous voyez les mêmes documents aux premières positions au sein de chaque tableau.

Les réponses sont suivies du tableau « value », qui comprend toujours des scores, des légendes et des champs récupérables par défaut. Si vous avez spécifié le paramètre select, le tableau « value » est limité aux champs que vous avez spécifiés. Pour plus d’informations sur les éléments de la réponse, consultez [Créer une requête sémantique](semantic-how-to-query-request.md).

Pour la requête « How do clouds form », la réponse suivante est renvoyée :

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Conseils pour la génération de réponses de haute qualité

Pour de meilleurs résultats, retournez les réponses sémantiques sur un corpus de documents qui présentent les caractéristiques suivantes :

+ « searchFields » doit inclure un ou plusieurs champs qui fournissent un texte suffisant dans lequel une réponse est susceptible d’être trouvée.

+ L’extraction et la synthèse sémantiques ont des limites sur la quantité de contenu qui peut être analysé dans un délai raisonnable. Collectivement, seuls les premiers 20 000 jetons sont analysés. Au-delà, rien ne sera pris en compte. En pratique, si vous avez des documents volumineux qui s’étendent sur des centaines de pages, essayez d’abord de scinder le contenu en éléments gérables.

+ Les chaînes de requête ne doivent pas être null (search=`*`) et la chaîne doit avoir les caractéristiques d’une question, par opposition à une recherche par mot clé (une liste séquentielle de termes ou d’expressions arbitraires). Si la chaîne de requête ne semble pas être une réponse, le traitement des réponses est ignoré, même si la demande spécifie « answers » en tant que paramètre de requête.

## <a name="next-steps"></a>Étapes suivantes

+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)
+ [Algorithme de classement sémantique](semantic-ranking.md)
+ [Algorithme de similarité](index-ranking-similarity.md)
+ [Créer une requête sémantique](semantic-how-to-query-request.md)