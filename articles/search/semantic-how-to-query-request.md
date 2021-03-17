---
title: Créer une requête sémantique
titleSuffix: Azure Cognitive Search
description: Définissez un type de requête sémantique pour attacher les modèles Deep Learning (apprentissage profond) au traitement des requêtes, en déduisant l’intention et le contexte dans le cadre du classement et de la pertinence de la recherche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 7f7a09b9e20b461a8a1e448bf4a7b0747a35fbb1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487140"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Créer une requête sémantique dans la Recherche cognitive

> [!IMPORTANT]
> Le type de requête sémantique est en préversion publique, disponible par le biais de l’API REST en préversion et du portail Azure. Les fonctionnalités d’évaluation sont proposées telles quelles, dans le cadre de [Conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pendant le lancement de la préversion initiale, la recherche sémantique n’est pas facturée. Pour plus d’informations, consultez [Disponibilité et tarifs](semantic-search-overview.md#availability-and-pricing).

Dans cet article, découvrez comment formuler une requête de recherche qui utilise le classement sémantique et qui produit des légendes et des réponses sémantiques.

Les requêtes sémantiques tendent à mieux fonctionner sur les index de recherche créés à partir d'un contenu riche en texte, comme les PDF ou les documents contenant de gros blocs de texte.

## <a name="prerequisites"></a>Prérequis

+ Service de recherche de niveau Standard (S1, S2, S3), situé dans l’une des régions suivantes : USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest. Si vous disposez déjà d’un service S1 ou supérieur dans l’une de ces régions, vous pouvez demander l’accès sans devoir créer un nouveau service.

+ Accès à la préversion de la recherche sémantique : [s’inscrire](https://aka.ms/SemanticSearchPreviewSignup)

+ Un index de recherche existant avec du contenu en anglais

+ Un client de recherche pour l’envoi de requêtes

  Le client de recherche doit prendre en charge les API REST en préversion dans la demande de requête. Vous pouvez utiliser [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) ou le code que vous avez modifié pour effectuer des appels REST vers les API en préversion. Vous pouvez également utiliser l’[Explorateur de recherche](search-explorer.md) du portail Azure pour soumettre une requête sémantique.

+ Une requête [Recherche dans des documents](/rest/api/searchservice/preview-api/search-documents) avec l’option sémantique et d’autres paramètres décrits dans cet article.

## <a name="whats-a-semantic-query"></a>Qu’est-ce qu’une requête sémantique ?

Dans la Recherche cognitive, une requête désigne une requête paramétrable qui détermine le traitement des requêtes et la forme de la réponse. Une *requête sémantique* ajoute des paramètres qui appellent le modèle de reclassement sémantique. Celui-ci peut évaluer le contexte et la signification des résultats, faire remonter les résultats les plus pertinents, et renvoyer des réponses et des légendes sémantiques.

La requête suivante est représentative d’une requête sémantique de base (sans réponse).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Comme pour toutes les requêtes dans la Recherche cognitive, la requête cible la collection de documents d’un seul index. Par ailleurs, une requête sémantique subit la même séquence de décomposition, d’analyse et de recherche qu’une requête non sémantique. La différence réside dans le calcul de la pertinence. Comme défini dans cette préversion, une requête sémantique désigne une requête dont les *résultats* sont retraités au moyen d’algorithmes élaborés, ce qui permet de faire émerger les correspondances considérées comme les plus pertinentes par le classement sémantique, plutôt que les scores attribués par l’algorithme de classement de similarité par défaut. 

Seules les 50 premières correspondances des résultats initiaux peuvent être classées de façon sémantique, et toutes incluent des légendes dans la réponse. Si vous le souhaitez, vous pouvez spécifier un paramètre **`answer`** dans la requête pour extraire une réponse potentielle. Ce modèle formule jusqu’à cinq réponses possibles à la requête, que vous pouvez choisir d’afficher en haut de la page de recherche.

## <a name="query-using-rest-apis"></a>Interroger à l’aide des API REST

Vous trouverez la spécification complète de l’API REST dans [Rechercher dans des documents (préversion REST)](/rest/api/searchservice/preview-api/search-documents).

Les requêtes sémantiques fournissent automatiquement des légendes et une mise en surbrillance. Si vous souhaitez que la réponse inclue des explications, vous pouvez ajouter un paramètre **`answer`** facultatif dans la requête. Ce paramètre, ainsi que la construction de la chaîne de requête proprement dite, produiront une réponse dans la réponse.

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

Ce paramètre est facultatif dans la mesure où aucune erreur ne survient si vous l'omettez, mais il est fortement recommandé de fournir une liste ordonnée de champs, tant pour les légendes que pour les réponses.

Le paramètre searchFields permet d'identifier les passages dont la « similarité sémantique » avec la requête doit être évaluée. Dans le cadre de la préversion, nous vous conseillons vivement de compléter les champs searchFields car le modèle a besoin d'une indication sur les champs les plus importants à traiter.

L'ordre des champs searchFields est essentiel. Si vous utilisez déjà searchFields dans des requêtes Lucene simples ou complètes, veillez à réexaminer ce paramètre lors du basculement vers un type de requête sémantique.

Suivez ces instructions pour obtenir des résultats optimaux lorsqu'au moins deux champs searchFields sont spécifiés :

+ Incluez uniquement les champs de type chaîne et les champs de type chaîne de niveau supérieur dans les collections. Si vous incluez des champs d'un autre type que ceux-ci dans une collection, aucune erreur ne survient, mais ces champs ne sont pas utilisés dans le cadre du classement sémantique.

+ Le premier champ doit toujours être concis (titre ou nom, par exemple), idéalement moins de 25 mots.

+ Si l'index contient un champ URL textuel (lisible par l'utilisateur tel que `www.domain.com/name-of-the-document-and-other-details` et non dicté par la machine tel que `www.domain.com/?id=23463&param=eis`), placez-le en deuxième position dans la liste (ou en première position en l'absence de champ de titre concis).

+ Faites suivre ces champs de champs descriptifs où la réponse aux requêtes sémantiques peut être trouvée (contenu principal d'un document, par exemple).

Si un seul champ est spécifié, utilisez un champ descriptif dans lequel la réponse aux requêtes sémantiques peut être trouvée (contenu principal d'un document, par exemple). Choisissez un champ qui fournit un contenu suffisant.

#### <a name="step-3-remove-orderby-clauses"></a>Étape 3 : Supprimer les clauses orderBy

Supprimez les clauses « orderBy » susceptibles de figurer dans une requête existante. Le score sémantique est utilisé pour trier les résultats, et si vous incluez une logique de tri explicite, une erreur HTTP 400 est retournée.

#### <a name="step-4-add-answers"></a>Étape 4 : Ajouter des réponses

Vous pouvez ajouter des réponses (« answers ») si vous souhaitez inclure un traitement supplémentaire permettant de fournir une réponse. Les réponses (et les légendes) sont formulées à partir de passages trouvés dans les champs répertoriés dans le paramètre searchFields. Veillez à inclure des champs riches en contenu dans searchFields pour obtenir les meilleures réponses et légendes possibles.

Les réponses reposent sur des conditions explicites et implicites. 

+ Les conditions explicites incluent l'ajout de « answers=extractive ». En outre, pour spécifier le nombre de réponses renvoyées dans la réponse globale, ajoutez « count » suivi d'un chiffre : `"answers=extractive|count=3"`.  La valeur par défaut est 1. La valeur maximale est 5.

+ Les conditions implicites incluent une construction de chaîne de requête qui se prête à une réponse. Une question composée de « quel hôtel contient une chambre verte » a plus de chances de donner lieu à une réponse qu'une requête composée d'une déclaration telle que « hôtel avec un intérieur chic ». Comme vous vous en doutez, la requête ne peut pas être nulle ou rester non spécifiée.

Le point important à retenir est que si la requête ne ressemble pas à une question, le traitement des réponses est ignoré, même si le paramètre « answers » est défini.

#### <a name="step-5-add-other-parameters"></a>Étape 5 : Ajouter d'autres paramètres

Définissez tous les autres paramètres que vous souhaitez inclure dans la requête. D’autres paramètres tels que [speller](speller-how-to-add.md), [select](search-query-odata-select.md) et « count » améliorent la qualité de la requête et la lisibilité de la réponse.

Si vous le souhaitez, vous pouvez personnaliser le style de surbrillance appliqué aux légendes. Les légendes appliquent le format de surbrillance aux passages importants dans le document qui résument la réponse. Par défaut, il s’agit de `<em>`. Si vous souhaitez spécifier le type de mise en forme (par exemple un arrière-plan jaune), vous pouvez définir highlightPreTag et highlightPostTag.

### <a name="review-the-response"></a>Passer en revue la réponse

La réponse à la requête ci-dessus retourne la correspondance suivante comme premier choix. Les légendes sont retournées automatiquement, avec les versions en texte brut et en surbrillance. Pour plus d’informations sur les réponses sémantiques, consultez [Classement sémantique et réponses](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Paramètres utilisés dans une requête sémantique

Le tableau suivant récapitule les paramètres de requête utilisés dans une requête sémantique afin de les afficher de manière globale. Pour obtenir la liste de tous les paramètres, consultez [Recherche dans des documents (préversion REST)](/rest/api/searchservice/preview-api/search-documents)

| Paramètre | Type | Description |
|-----------|-------|-------------|
| queryType | String | Les valeurs valides sont « simple », « full » et « semantic ». La valeur « semantic » est requise pour les requêtes sémantiques. |
| queryLanguage | String | Obligatoire pour les requêtes sémantiques. Actuellement, seul « en-us » est implémenté. |
| searchFields | String | Liste délimitée par des virgules des champs pouvant faire l’objet d’une recherche. Facultatif, mais recommandé. Spécifie les champs sur lesquels le classement sémantique opère. </br></br>Contrairement aux types requête simple et requête complète, l’ordre dans lequel les champs sont répertoriés détermine la priorité.|
| answers |String | Champ facultatif pour spécifier que les réponses sémantiques doivent être incluses dans le résultat ou pas. Actuellement, seule l’option « extractive » est implémentée. Les réponses peuvent être configurées pour être retournées au nombre de cinq maximum. La valeur par défaut est 1. Cet exemple montre un nombre de trois réponses : "extractive\|count3"`. |

## <a name="query-with-search-explorer"></a>Exécuter des requêtes avec l’Explorateur de recherche

La requête suivante cible l’exemple d’index intégré des hôtels à l’aide de l’API version 2020-06-30-Preview, et s’exécute dans l’Explorateur de recherche. La clause `$select` limite les résultats à quelques champs seulement, ce qui facilite l’analyse du code JSON détaillé dans l’Explorateur de recherche.

### <a name="with-querytypesemantic"></a>Avec queryType=semantic

```json
search=nice hotel on water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

Les premiers résultats sont les suivants.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>Avec queryType (valeur par défaut)

À des fins de comparaison, exécutez la même requête que ci-dessus, en supprimant `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags`. Remarquez qu’il n’y a aucun `"@search.rerankerScore"` dans ces résultats et que différents hôtels apparaissent dans les trois premières positions.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Étapes suivantes

Rappelez-vous que le classement sémantique et les réponses sont générés à partir d’un jeu de résultats initial. Toute logique qui améliore la qualité des résultats initiaux sera reportée sur la recherche sémantique. À l’étape suivante, examinez les fonctionnalités qui participent aux résultats initiaux (y compris les analyseurs affectant la façon dont les chaînes sont tokenisées), les profils de score pouvant affiner les résultats et l’algorithme de pertinence par défaut.

+ [Analyseurs du traitement de texte](search-analyzers.md)
+ [Similarité et scoring dans la Recherche cognitive Azure](index-similarity-and-scoring.md)
+ [Ajouter des profils de scoring](index-add-scoring-profiles.md)
+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)
+ [Ajouter une vérification orthographique aux termes de la requête](speller-how-to-add.md)
