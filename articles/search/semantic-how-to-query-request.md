---
title: Créer une requête sémantique
titleSuffix: Azure Cognitive Search
description: Définissez un type de requête sémantique pour attacher les modèles Deep Learning (apprentissage profond) au traitement des requêtes, en déduisant l’intention et le contexte dans le cadre du classement et de la pertinence de la recherche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654660"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Créer une requête pour les légendes sémantiques dans Recherche cognitive

> [!IMPORTANT]
> La recherche sémantique est en préversion publique, disponible via l’API REST en préversion et le portail Azure. Les fonctionnalités d’évaluation sont proposées telles quelles, dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Ces fonctionnalités sont facturables. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

Cet article explique comment formuler une requête de recherche qui utilise un classement sémantique et retourne des légendes sémantiques (et éventuellement des [réponses sémantiques](semantic-answers.md)), avec mise en évidence des termes et expressions les plus pertinents. Tant les légendes que les réponses sont retournées dans des requêtes formulées à l’aide du type de requête « sémantique ».

Les légendes et réponses sont extraites du texte littéral du document de recherche. Le sous-système sémantique détermine quelle partie de votre contenu présente les caractéristiques d’une légende ou d’une réponse, mais ne compose pas de nouvelles phrases ou expressions. Pour cette raison, le contenu qui comprend des explications ou des définitions est le mieux adapté à la recherche sémantique.

## <a name="prerequisites"></a>Prérequis

+ Service de recherche de niveau Standard (S1, S2, S3), situé dans l’une des régions suivantes : USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest. Si vous disposez déjà d’un service S1 ou supérieur dans l’une de ces régions, vous pouvez demander l’accès sans devoir créer un nouveau service.

+ Accès à la préversion de la recherche sémantique : [s’inscrire](https://aka.ms/SemanticSearchPreviewSignup)

+ Un index de recherche existant avec du contenu en anglais

+ Un client de recherche pour l’envoi de requêtes

  Le client de recherche doit prendre en charge les API REST en préversion dans la demande de requête. Vous pouvez utiliser [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) ou du code effectuant des appels REST aux API en préversion. Vous pouvez également utiliser l’[Explorateur de recherche](search-explorer.md) du portail Azure pour soumettre une requête sémantique.

+ Une [requête d’interrogation](/rest/api/searchservice/preview-api/search-documents) doit inclure l’option sémantique et d’autres paramètres décrits dans cet article.

## <a name="whats-a-semantic-query"></a>Qu’est-ce qu’une requête sémantique ?

Dans la Recherche cognitive, une requête désigne une requête paramétrable qui détermine le traitement des requêtes et la forme de la réponse. Une *requête sémantique* ajoute des paramètres qui appellent le modèle de reclassement sémantique. Celui-ci peut évaluer le contexte et la signification des résultats, faire remonter les résultats les plus pertinents, et renvoyer des réponses et des légendes sémantiques.

La requête suivante est représentative d’une requête sémantique minimale (sans réponse).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Comme pour toutes les requêtes dans la Recherche cognitive, la requête cible la collection de documents d’un seul index. Par ailleurs, une requête sémantique subit la même séquence de décomposition, d’analyse, de recherche et de scoring qu’une requête non sémantique. 

La différence réside dans la pertinence et le scoring. Comme défini dans cette préversion, une requête sémantique désigne une requête dont les *résultats* sont reclassés au moyen d’un modèle de langage sémantique, ce qui permet de faire émerger les correspondances considérées comme les plus pertinentes par le classement sémantique, plutôt que les scores attribués par l’algorithme de classement de similarité par défaut.

Seules les 50 premières correspondances des résultats initiaux peuvent être classées de façon sémantique, et toutes incluent des légendes dans la réponse. Si vous le souhaitez, vous pouvez spécifier un paramètre **`answer`** dans la requête pour extraire une réponse potentielle. Pour plus d’informations, consultez [Réponses sémantiques](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Exécuter des requêtes avec l’Explorateur de recherche

L’[Explorateur de recherche](search-explorer.md) a été mis à jour pour inclure des options pour les requêtes sémantiques. Ces options deviennent visibles dans le portail après accomplissement des étapes suivantes :

1. [Inscription](https://aka.ms/SemanticSearchPreviewSignup) et admission de votre service de recherche dans le programme en préversion

1. Ouvrez le portail avec la syntaxe suivante : `https://portal.azure.com/?feature.semanticSearch=true`

Les options de requête incluent des commutateurs pour activer des requêtes sémantiques, des searchFields et une correction orthographique. Vous pouvez également coller les paramètres de requête requis dans la chaîne de requête.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Options de requête dans l’Explorateur de recherche" border="true":::

## <a name="query-using-rest"></a>Requête utilisant REST

Utilisez la [recherche de documents (préversion REST)](/rest/api/searchservice/preview-api/search-documents) pour formuler la requête par programme.

Une réponse comprend des sous-titres et une mise en surbrillance automatique. Si vous souhaitez que la réponse inclue la correction orthographique ou des explications, vous pouvez ajouter un paramètre **`speller`** ou **`answers`** facultatif dans la requête.

L’exemple suivant utilise l’index hotels-sample-index pour créer une demande de requête sémantique, avec des réponses et des légendes sémantiques :

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Le tableau suivant récapitule les paramètres de requête utilisés dans une requête sémantique afin de les afficher de manière globale. Pour obtenir la liste de tous les paramètres, consultez [Recherche dans des documents (préversion REST)](/rest/api/searchservice/preview-api/search-documents)

| Paramètre | Type | Description |
|-----------|-------|-------------|
| queryType | String | Les valeurs valides sont « simple », « full » et « semantic ». La valeur « semantic » est requise pour les requêtes sémantiques. |
| queryLanguage | String | Obligatoire pour les requêtes sémantiques. Actuellement, seul « en-us » est implémenté. |
| searchFields | String | Liste délimitée par des virgules des champs pouvant faire l’objet d’une recherche. Spécifie les champs sur lesquels le classement sémantique se produit, à partir desquels les légendes et réponses sont extraites. </br></br>Contrairement aux types requête simple et requête complète, l’ordre dans lequel les champs sont répertoriés détermine la priorité. Pour plus d’informations sur l’utilisation, reportez-vous à [Étape 2 : définir searchFields](#searchfields). |
| Vérificateur d’orthographe | String | Paramètre facultatif, non spécifique aux requêtes sémantiques, qui corrige les termes mal orthographiés avant qu’ils n’atteignent le moteur de recherche. Pour plus d’informations, consultez [Ajouter une correction orthographique à des requêtes](speller-how-to-add.md). |
| answers |String | Paramètres facultatifs indiquant que les réponses sémantiques doivent être incluses dans le résultat ou pas. Actuellement, seule l’option « extractive » est implémentée. Les réponses peuvent être configurées pour être retournées au nombre de cinq maximum. La valeur par défaut est 1. Cet exemple montre un nombre de trois réponses : "extractive\|count3"`. Pour plus d’informations, consultez [Retourner des réponses sémantiques](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formuler la requête

Cette section décrit les paramètres de requête nécessaires à la recherche sémantique.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Étape 1 : Définir queryType et queryLanguage

Ajoutez les paramètres suivants au reste. Ces deux paramètres sont obligatoires.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

Le paramètre queryLanguage doit être cohérent avec tous les [analyseurs linguistiques](index-add-language-analyzers.md) affectés aux définitions de champ dans le schéma d’index. Si la valeur de queryLanguage est « en-us », tous les analyseurs de langue doivent également être une variante anglaise (« en.microsoft » ou « en.lucene »). Un analyseur sans langue spécifiée, tels que l’analyseur de mots-clés ou simple, ne présente aucun conflit avec des valeurs de queryLanguage.

Dans une demande de requête, si vous utilisez également la [correction orthographique](speller-how-to-add.md), le paramètre queryLanguage que vous définissez s’applique aussi bien au vérificateur d’orthographe qu’aux réponses et aux légendes. Il n’existe pas de remplacement pour certaines parties. 

Même si le contenu d’un index de recherche peut inclure plusieurs langues, l’entrée de requête est généralement formulée en une langue unique. Le moteur de recherche ne vérifie pas la compatibilité du paramètre queryLanguage, de l’analyseur linguistique et de la langue dans laquelle le contenu est composé. Veillez donc à limiter les requêtes en conséquence pour éviter la production de résultats incorrects.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Étape 2 : Définir searchFields

Le paramètre searchFields permet d'identifier les passages dont la « similarité sémantique » avec la requête doit être évaluée. Dans le cadre de la préversion, nous vous conseillons vivement de compléter les champs searchFields car le modèle a besoin d'une indication sur les champs les plus importants à traiter.

L'ordre des champs searchFields est essentiel. Si vous utilisez déjà searchFields dans le code existant pour des requêtes Lucene simples ou complètes, réexaminez ce paramètre pour contrôler l’ordre des champs lors du basculement vers un type de requête sémantique.

Pour deux searchFields ou plus :

+ Incluez uniquement les champs de type chaîne et les champs de type chaîne de niveau supérieur dans les collections. Si vous incluez des champs d'un autre type que ceux-ci dans une collection, aucune erreur ne survient, mais ces champs ne sont pas utilisés dans le cadre du classement sémantique.

+ Le premier champ doit toujours être concis (titre ou nom, par exemple), idéalement moins de 25 mots.

+ Si l’index contient un champ URL textuel (lisible par l’utilisateur tel que `www.domain.com/name-of-the-document-and-other-details` et non dicté par la machine tel que `www.domain.com/?id=23463&param=eis`), placez-le en deuxième position dans la liste (ou en première position en l’absence de champ de titre concis).

+ Faites suivre ces champs de champs descriptifs où la réponse aux requêtes sémantiques peut être trouvée (contenu principal d'un document, par exemple).

Si un seul champ est indiqué, utilisez un champ descriptif dans lequel la réponse aux requêtes sémantiques peut être trouvée (contenu principal d’un document, par exemple). 

#### <a name="step-3-remove-orderby-clauses"></a>Étape 3 : Supprimer les clauses orderBy

Supprimez les clauses « orderBy » susceptibles de figurer dans une requête existante. Le score sémantique est utilisé pour trier les résultats, et si vous incluez une logique de tri explicite, une erreur HTTP 400 est retournée.

#### <a name="step-4-add-answers"></a>Étape 4 : ajouter des réponses

Vous pouvez ajouter des réponses (« answers ») si vous souhaitez inclure un traitement supplémentaire permettant de fournir une réponse. Les réponses (et les légendes) sont extraites à partir de passages trouvés dans les champs répertoriés dans le paramètre searchFields. Veillez à inclure des champs riches en contenu dans searchFields pour obtenir les meilleures réponses possibles. Pour plus d’informations, consultez [Guide pratique pour retourner des réponses sémantiques](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>Étape 5 : Ajouter d'autres paramètres

Définissez tous les autres paramètres que vous souhaitez inclure dans la requête. D’autres paramètres tels que [speller](speller-how-to-add.md), [select](search-query-odata-select.md) et « count » améliorent la qualité de la requête et la lisibilité de la réponse.

Si vous le souhaitez, vous pouvez personnaliser le style de surbrillance appliqué aux légendes. Les légendes appliquent le format de surbrillance aux passages importants dans le document qui résument la réponse. Par défaut, il s’agit de `<em>`. Si vous souhaitez spécifier le type de mise en forme (par exemple un arrière-plan jaune), vous pouvez définir highlightPreTag et highlightPostTag.

## <a name="evaluate-the-response"></a>Évaluer la réponse

Comme pour toutes les requêtes, une réponse se compose de tous les champs marqués comme récupérables, ou seulement des champs listés dans le paramètre de sélection. Elle inclut le score de pertinence d’origine et peut également inclure un nombre, ou des résultats par lot, en fonction de la façon dont vous avez formulé la demande.

Dans une requête sémantique, la réponse possède des éléments supplémentaires : un nouveau score de pertinence sémantiquement classé, des légendes en texte brut et des mises en surbrillance, voire une réponse.

Dans une application cliente, vous pouvez structurer la page de recherche pour inclure une légende comme description de la correspondance, plutôt que l’intégralité du contenu d’un champ spécifique. Cela est utile lorsque les champs individuels sont trop denses pour la page des résultats de la recherche.

La réponse à la requête ci-dessus retourne la correspondance suivante comme premier choix. Les légendes sont retournées automatiquement, avec les versions en texte brut et en surbrillance. Les réponses sont omises de l’exemple, car il n’est pas possible de les déterminer pour cette requête et ce corpus.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Étapes suivantes

Rappelez-vous que le classement sémantique et les réponses sont générés à partir d’un jeu de résultats initial. Toute logique qui améliore la qualité des résultats initiaux sera reportée sur la recherche sémantique. À l’étape suivante, examinez les fonctionnalités qui participent aux résultats initiaux (y compris les analyseurs affectant la façon dont les chaînes sont tokenisées), les profils de score pouvant affiner les résultats et l’algorithme de pertinence par défaut.

+ [Analyseurs du traitement de texte](search-analyzers.md)
+ [Algorithme de classement de similarité](index-similarity-and-scoring.md)
+ [Profils de score](index-add-scoring-profiles.md)
+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)
+ [Algorithme de classement sémantique](semantic-ranking.md)
