---
title: Utiliser la syntaxe de requête Lucene simple
titleSuffix: Azure Cognitive Search
description: Exemples de requêtes illustrant la syntaxe simple pour effectuer des recherches en texte intégral, filtrées et géographiques sur un index Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694034"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Utiliser la syntaxe de recherche « simple » dans Recherche cognitive Azure

Dans Recherche cognitive Azure, la [syntaxe de requête simple](query-simple-syntax.md) appelle l’analyseur de requêtes par défaut pour une recherche en texte intégral. Cet analyseur rapide gère des scénarios courants, notamment la recherche en texte intégral, la recherche filtrée et à facettes, ainsi que la recherche de préfixe. Cet article utilise des exemples pour illustrer l’utilisation de la syntaxe simple dans une demande [Rechercher des documents (API REST)](/rest/api/searchservice/search-documents).

> [!NOTE]
> Une autre syntaxe de requête est la syntaxe [Lucene complète](query-lucene-syntax.md), qui prend en charge des structures de requête plus complexes, telles qu’une recherche approximative et par caractères génériques. Pour plus d’informations et d’exemples, consultez [Utiliser la syntaxe Lucene complète](search-query-lucene-examples.md).

## <a name="hotels-sample-index"></a>Index hotels-sample

Les requêtes suivantes sont basées sur l’index hotels-sample, que vous pouvez créer en suivant les instructions de ce guide de [démarrage rapide](search-get-started-portal.md).

Les exemples de requêtes sont formulés à l’aide de l’API REST et des requêtes POST. Vous pouvez les coller et les exécuter dans [Postman](search-get-started-rest.md) ou dans [Visual Studio Code grâce à l’extension Recherche cognitive](search-get-started-vs-code.md).

Les en-têtes de requête doivent comporter les valeurs suivantes :

| Clé | Valeur |
|-----|-------|
| Content-Type | application/json|
| api-key  | `<your-search-service-api-key>`, clé de requête ou d’administration |

Les paramètres URI doivent inclure le point de terminaison de votre service de recherche avec le nom de l’index, les collections de documents, la commande de recherche et la version de l’API, comme dans l’exemple suivant :

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

Le corps de la demande doit être formé comme un JSON valide :

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ « search » défini sur `*` est une requête non spécifiée équivalente à une recherche null ou vide. Elle n’est pas particulièrement utile, mais c’est la recherche la plus simple que vous puissiez effectuer, et elle affiche tous les champs récupérables dans l’index, avec toutes les valeurs.

+ « queryType » défini sur « simple » est la valeur par défaut et peut être omis. Il est toutefois inclus pour renforcer le fait que les exemples de requêtes de cet article sont formulés dans la syntaxe simple.

+ « select » défini sur une liste de champs séparés par des virgules est utilisé pour la composition des résultats de la recherche, y compris uniquement les champs qui sont utiles pour les résultats de la recherche.

+ « count » renvoie le nombre de documents correspondant aux critères de recherche. Une chaîne de recherche vide comptabilisera tous les documents figurant dans l’index (environ 50 pour l’index hotels-sample).

## <a name="example-1-full-text-search"></a>Exemple 1 : Recherche en texte intégral

La recherche en texte intégral peut porter sur n’importe quel nombre de termes autonomes ou d’expressions entre guillemets, avec ou sans opérateurs booléens. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

Une recherche par mot-clé constituée d’expressions ou de termes importants a tendance à donner de meilleurs résultats. Les champs de chaîne font l’objet d’une analyse de texte lors de l’indexation et de l’interrogation, ce qui permet de supprimer les mots non essentiels comme « the », « and », « it ». Pour voir comment une chaîne de requête est mise sous forme de jeton dans l’index, transmettez la chaîne dans un appel [d’analyse de texte](/rest/api/searchservice/test-analyzer) à l’index.

Le paramètre « searchMode » contrôle la précision et le rappel. Si vous voulez plus de rappel, utilisez la valeur par défaut «any », qui renvoie un résultat si une partie de la chaîne de requête est mise en correspondance. Si vous privilégiez la précision, c’est-à-dire que toutes les parties de la chaîne sont mises en correspondance, remplacez searchMode par « all ». Testez les deux méthodes ci-dessus pour voir dans quelle mesure le mode de recherche modifie le résultat.

La réponse à la requête « pool spa +airport » doit être similaire à l’exemple suivant, ajusté par souci de concision.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Vous pouvez remarquer le score de recherche dans la réponse. Il s’agit du score de pertinence de la correspondance. Par défaut, un service de recherche renvoie les 50 premières correspondances en fonction de ce score.

Des scores uniformes de « 1.0 » sont obtenus en l’absence de classement, soit parce que la recherche n’était pas une recherche en texte intégral, soit parce qu’aucun critère n’a été fourni. Par exemple, pour une recherche vide (search=`*`), les lignes sont renvoyées dans un ordre arbitraire. Si vous incluez des critères réels, vous constaterez que les scores de recherche deviendront des valeurs significatives.

## <a name="example-2-look-up-by-id"></a>Exemple 2 : Recherche par ID

Lorsque vous retournez des résultats de recherche dans une requête, l’étape logique suivante consiste à fournir une page de détails qui comprend davantage de champs du document. Cet exemple vous indique comment retourner un document unique à l’aide du [document de recherche](/rest/api/searchservice/lookup-document) en transmettant l’ID du document.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Tous les documents ont un identificateur unique. Si vous utilisez le portail, sélectionnez l’index dans l’onglet **Index**, puis examinez les définitions des champs pour déterminer quel champ est la clé. À l’aide de REST, l’appel [Obtenir un index](/rest/api/searchservice/get-index) renvoie la définition de l’index dans le corps de la réponse.

La réponse à la requête ci-dessus consiste en un document dont la clé est 41. Tout champ marqué comme « récupérable » dans la définition de l’index peut être renvoyé dans les résultats de recherche et affiché dans votre application.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Exemple 3 : Filtre de texte

La [syntaxe de filtre](search-query-odata-filter.md) est une expression OData que vous pouvez utiliser de façon autonome ou avec « search ». Utilisés conjointement, le filtre est d’abord appliqué à la totalité de l’index et la recherche est ensuite effectuée sur les résultats du filtre. Les filtres peuvent donc être utiles pour améliorer les performances des requêtes, puisqu’ils limitent le nombre de documents que devra traiter la requête de recherche.

Les filtres peuvent être définis sur n’importe quel champ marqué comme « filtrable » dans la définition de l’index. Pour l’index hotels-sample, les champs filtrables comprennent les champs Category, Tags, ParkingIncluded, Rating et la plupart des champs Address.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

La réponse à la requête ci-dessus est limitée aux hôtels de la catégorie « Resort and Spa », qui comprennent les termes « art » ou « tours ». Dans ce cas, il n’y a qu’une seule correspondance.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Exemple 4 : Fonctions de filtre

Les expressions de filtre peuvent inclure [des fonctions « search.ismatch » et « search.ismatchscoring »](search-query-odata-full-text-search-functions.md), ce qui vous permet de créer une requête de recherche dans le filtre. Cette expression de filtre utilise un caractère générique sur *gratuit* pour sélectionner des équipements, y compris le Wi-Fi gratuit, le stationnement gratuit, etc.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

La réponse à la requête ci-dessus renvoie 19 hôtels qui offrent des services gratuits. Notez que le score de recherche est un « 1.0 » uniforme dans les résultats. Cela s’explique par le fait que l’expression de recherche est null ou vide, ce qui donne lieu à des correspondances verbatim, mais pas à une recherche en texte intégral. Les scores de pertinence sont renvoyés uniquement lors d’une recherche en texte intégral. Si vous utilisez des filtres sans « search », assurez-vous d’avoir suffisamment de champs triables pour pouvoir contrôler le classement de la recherche.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Exemple 5 : Filtres de plage

Le filtrage de plage est pris en charge par des expressions de filtre pour n’importe quel type de données. Les exemples suivants illustrent des plages numériques et de chaînes. Les types de données sont importants dans les filtres de plage et fonctionnent mieux lorsque les données numériques se trouvent dans des champs numériques, et les données de chaîne dans des champs de chaîne. L’utilisation de données numériques dans les champs de chaîne n’est pas adaptée aux plages, car les chaînes numériques ne sont pas comparables.

La requête suivante est une plage numérique. Dans l’index hotels-sample, le seul champ numérique filtrable est le classement.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

La réponse à cette requête doit être similaire à l’exemple suivant, ajusté par souci de concision.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

La requête suivante est un filtre de plage sur un champ de chaîne (Address/StateProvince) :

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

La réponse à cette requête doit être similaire à l’exemple ci-dessous, ajusté par souci de concision. Dans cet exemple, il n’est pas possible d’utiliser le champ StateProvince pour le tri, car il n’est pas marqué comme « triable » dans la définition de l’index.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Exemple 6 : Recherche basée sur la localisation

L’index hotels-sample inclut un champ geo_location avec des coordonnées de latitude et de longitude. Cet exemple utilise la [fonction geo.distance](search-query-odata-geo-spatial-functions.md#examples) qui applique un filtre sur les documents situés à une distance arbitraire (en kilomètres) d’un point de départ que vous spécifiez. Vous pouvez ajuster la dernière valeur de la requête (10) pour réduire ou étendre la surface d’exposition de la requête.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

La réponse à cette requête renvoie tous les hôtels situés dans un rayon de 10 kilomètres des coordonnées fournies :

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Exemple 7 : Opérateurs booléens avec searchMode

La syntaxe simple prend en charge les opérateurs booléens sous la forme de caractères (`+, -, |`) pour gérer la logique de requête AND, OR et NOT. La recherche booléenne se déroule comme on peut s’y attendre, à quelques exceptions près. 

Dans les exemples précédents, le paramètre « searchMode » a été utilisé pour influencer la précision et le rappel, « searchMode=any » favorisant le rappel (un document qui remplit l’un des critères est considéré comme une correspondance), et « searchMode=all » favorisant la précision (tous les critères doivent être mis en correspondance dans un document). 

Dans une recherche booléenne, la valeur par défaut « searchMode=any » peut prêter à confusion si vous placez plusieurs opérateurs dans une requête et obtenez des résultats moins affinés que prévu. Cela est particulièrement vrai avec l’opérateur NOT, les résultats incluant tous les documents « ne contenant pas » un terme ou une expression spécifique.

L'exemple suivant illustre cette situation. Si vous lancez la requête suivante avec searchMode (any), 42 documents seront renvoyés : ceux qui contiennent le terme « restaurant », et ceux qui ne contiennent pas l’expression « climatisation ». 

Notez qu’il n’y a aucun espace entre l’opérateur booléen (`-`) et l’expression « climatisation ».

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Affecter la valeur « searchMode=all » applique un effet cumulé aux critères et retourne un jeu de résultats plus petit (7 correspondances) constitué de documents contenant le terme « restaurant », à l’exception de ceux contenant l’expression « climatisation ».

La réponse à cette requête est désormais similaire à l’exemple suivant, ajusté par souci de concision.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Exemple 8: Résultats de pagination

Grâce aux exemples précédents, vous avez découvert les paramètres qui ont une incidence sur la composition des résultats de recherche, notamment le paramètre « select », qui détermine quels champs se trouvent dans un résultat, les ordres de tri et comment inclure le nombre de correspondances. Cet exemple est la suite de la composition des résultats de la recherche sous la forme de paramètres de pagination qui vous permettent de traiter par lot le nombre de résultats qui s’affichent dans une page donnée. 

Par défaut, un service de recherche renvoie les 50 premières correspondances. Pour contrôler le nombre de correspondances de chaque page, utilisez le paramètre « top » pour définir la taille du lot, puis « skip » pour récupérer les lots suivants.

L’exemple suivant utilise un filtre et un ordre de tri sur le champ Rating (filtrable et triable), car il est plus facile de voir les effets de la pagination sur les résultats triés. Lors d’une requête de recherche complète normale, les principales correspondances sont classées et paginées par « @search.score ».

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

La requête a trouvé 21 documents correspondants, mais étant donné que vous avez spécifié « top », la réponse renvoie uniquement les cinq premières correspondances, le classement étant compris entre 4,7 et 4,9 avec « Lady of the Lake B & B ». 

Pour obtenir les cinq suivants, ignorez le premier lot :

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

La réponse du deuxième lot ignore les cinq premières correspondances, et renvoie les cinq suivantes, en commençant par « Pull’r Inn Motel ». Pour continuer avec des lots supplémentaires, conservez « top » sur 5, puis incrémentez « skip » de 5 à chaque nouvelle demande (skip=5, skip=10, skip=15, etc.).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez un peu plus sur la syntaxe de base des requêtes, vous pouvez essayer de spécifier les requêtes dans le code. Les liens suivants expliquent comment configurer des requêtes de recherche à l’aide des Kits de développement logiciel (SDK) Azure.

+ [Interroger un index à l’aide du SDK .NET](search-get-started-dotnet.md)
+ [Interroger un index à l’aide du SDK Python](search-get-started-python.md)
+ [Interroger un index à l’aide du SDK JavaScript](search-get-started-javascript.md)

Vous trouverez des informations de référence supplémentaires sur la syntaxe et sur l’architecture de requête, ainsi que des exemples, en cliquant sur les liens suivants :

+ [Exemples de syntaxe de requête Lucene pour créer des requêtes avancées](search-query-lucene-examples.md)
+ [Fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md)
+ [Syntaxe de requête simple](query-simple-syntax.md)
+ [Syntaxe de requête complète Lucene](query-lucene-syntax.md)
+ [Syntaxe du filtre](search-query-odata-filter.md)