---
title: Retourner une réponse sémantique
titleSuffix: Azure Cognitive Search
description: Décrit la composition d’une réponse sémantique et l’obtention de réponses à partir d’un jeu de résultats.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: d0390bd70080ea0174a81cce9538396321dec658
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111539359"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Retourner une réponse sémantique dans Recherche cognitive Azure

> [!IMPORTANT]
> La fonctionnalité de recherche sémantique en préversion publique est sujette à des [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Elle est disponible via le portail Azure, une API REST en préversion et des kits de développement logiciel (SDK) bêta. Ces fonctionnalités sont facturables. Pour plus d’informations, consultez [Disponibilité et tarification](semantic-search-overview.md#availability-and-pricing).

En appelant [le classement sémantique et les légendes](semantic-how-to-query-request.md), vous pouvez extraire des documents correspondant le mieux à votre requête du contenu « répondant » directement à celle-ci. Une ou plusieurs réponses peuvent être incluses, et vous pouvez ensuite les afficher sur une page de recherche pour améliorer l’expérience utilisateur de votre application.

Cet article explique comment demander une réponse sémantique et dépaqueter la réponse, et décrit les caractéristiques de contenu les plus propices à la production de réponses de qualité.

## <a name="prerequisites"></a>Prérequis

Tous les prérequis qui s’appliquent aux [requêtes sémantiques](semantic-how-to-query-request.md#prerequisites) s’appliquent également aux réponses, y compris [le niveau de service et la région](semantic-search-overview.md#availability-and-pricing).

+ La logique de requête doit inclure les paramètres de requête sémantique « queryType=semantic », ainsi que le paramètre « answers ». Les paramètres requis sont décrits dans cet article.

+ Les chaînes de requête saisie par l’utilisateur doivent être reconnaissables en tant que questions (quoi, où, quand, comment).

+ Les documents de recherche dans l’index doivent contenir du texte présentant les caractéristiques d’une réponse, et ce texte doit exister dans un des champs répertoriés dans « searchFields ». Par exemple, dans le cas d’une requête « qu’est-ce qu’une table de hachage », si aucun des champs de recherche ne contient de un passage incluant « Une table de hachage est... », il est peu probable qu’une réponse soit retournée.

## <a name="what-is-a-semantic-answer"></a>Qu’est-ce qu’une réponse sémantique ?

Une réponse sémantique est une sous-structure d’une [réponse à une requête sémantique](semantic-how-to-query-request.md). Elle se compose d’un ou de plusieurs passages textuels d’un document de recherche, formulés comme une réponse à une requête qui ressemble à une question. Pour qu’une réponse soit renvoyée, des expressions ou des phrases doivent exister dans un document de recherche ayant les caractéristiques de langage d’une réponse, et la requête elle-même doit être posée comme une question.

Le service Recherche cognitive utilise un modèle de compréhension par lecture automatique pour choisi la meilleur réponse. Le modèle produit un ensemble de réponses potentielles à partir du contenu disponible et, quand il atteint un niveau de confiance suffisamment élevé, il propose une réponse.

Les réponses sont retournées sous la forme d’un objet indépendant de niveau supérieur dans la charge utile de réponse à la requête que vous pouvez choisir de rendre sur les pages de recherche, ainsi que les résultats de la recherche. Structurellement, il s’agit d’un élément de tableau dans la réponse, qui comprend du texte, une clé de document et un score de confiance.

<a name="query-params"></a>

## <a name="how-to-specify-answers-in-a-query-request"></a>Comment spécifier des « réponses » dans une demande de requête

Pour renvoyer une réponse sémantique, la requête doit inclure les paramètres sémantiques « queryType », « queryLanguage », « searchFields » et « answers ». La spécification du paramètre « answers » ne garantit pas que vous obtiendrez une réponse, mais la requête doit inclure ce paramètre pour que le traitement de la réponse soit appelé.

Le paramètre « searchFields » est crucial pour renvoyer une réponse de haute qualité, en termes tant de contenu que d’ordre (voir ci-dessous). 

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

+ Une chaîne de requête ne doit pas être null et doit être formulée comme question.

+ Le paramètre « queryType » (type de requête) doit être « semantic » (sémantique).

+ Le paramètre « queryLanguage » (langue de requête) doit être l’une des valeurs de la [liste des langues prises en charge (API REST)](/rest/api/searchservice/preview-api/search-documents#queryLanguage).

+ Le paramètre « searchFields » (champs de recherche) détermine les champs qui fournissent des jetons, ou segments de texte, au modèle d’extraction. Les mêmes champs qui produisent des légendes produisent également des réponses. Pour obtenir des conseils précis sur la façon de définir ce champ de manière à ce qu’il fonctionne tant pour les légendes que pour les réponses, consultez [Définir searchFields](semantic-how-to-query-request.md#searchfields). 

+ Pour « answers », la construction de paramètre est `"answers": "extractive"`, où le nombre de réponses par défaut retourné est un. Vous pouvez augmenter le nombre de réponses en ajoutant une valeur de `count`, comme dans l’exemple ci-dessus, jusqu’à cinq.  Le fait que vous ayez besoin de plusieurs réponses dépend de l’expérience utilisateur de votre application et de la façon dont vous souhaitez afficher les résultats.

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
    ]
        }
}

```

## <a name="tips-for-producing-high-quality-answers"></a>Conseils pour la génération de réponses de haute qualité

Pour de meilleurs résultats, retournez les réponses sémantiques sur un corpus de documents qui présentent les caractéristiques suivantes :

+ « searchFields » doit inclure des champs fournissant suffisamment de texte pour qu’il soit possible d’y trouver une réponse. Seul le texte exact d’un document peut apparaître en tant que réponse.

+ Les chaînes de requête ne doivent pas être null (search=`*`) et la chaîne doit avoir les caractéristiques d’une question, par opposition à une recherche par mot clé (une liste séquentielle de termes ou d’expressions arbitraires). Si la chaîne de requête ne semble pas être une réponse, le traitement des réponses est ignoré, même si la demande spécifie « answers » en tant que paramètre de requête.

+ L’extraction et la synthèse sémantiques sont limitées quand à la quantité de jetons par document qui peuvent être analysés dans un délai raisonnable. En pratique, si vous avez des documents volumineux qui s’étendent sur des centaines de pages, vous devriez commencer par tenter de scinder le contenu en documents de plus petite taille.

## <a name="next-steps"></a>Étapes suivantes

+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)
+ [Algorithme de classement sémantique](semantic-ranking.md)
+ [Algorithme de classement de similarité](index-ranking-similarity.md)
+ [Créer une requête sémantique](semantic-how-to-query-request.md)
