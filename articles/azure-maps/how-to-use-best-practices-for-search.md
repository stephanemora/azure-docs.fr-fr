---
title: Comment rechercher efficacement à l’aide d’Azure Maps | Microsoft Docs
description: Découvrez comment utiliser les meilleures pratiques pour la recherche à l’aide d’Azure Maps
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f7a14e975a5ca3aee5588f55f43b28081c100074
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358153"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Meilleures pratiques pour utiliser le Service de recherche Azure Maps

Azure Maps [Service de recherche](https://docs.microsoft.com/rest/api/maps/search) inclut des API avec différentes fonctionnalités, par exemple, à partir de la recherche d’adresse à la recherche de données de Point d’intérêt (POI) autour d’un emplacement spécifique. Dans cet article, nous partagerons les meilleures pratiques pour appeler des données via les Services de recherche Azure Maps. Vous apprendrez à :

* Générer des requêtes pour renvoyer les correspondances pertinentes
* Limiter les résultats de recherche
* Découvrez la différence entre les différents types de résultats
* Lire la structure de réponse de recherche adresse


## <a name="prerequisites"></a>Conditions préalables

Pour appeler les API du service Maps, vous avez besoin d’un compte et d’une clé Maps. Pour plus d’informations sur la création d’un compte et la récupération d’une clé, consultez [How to manage your Azure Maps account and keys](how-to-manage-account-keys.md) (Gérer votre compte et vos clés Azure Maps).

> [!Tip]
> Pour interroger le service de recherche, vous pouvez utiliser la [application Postman](https://www.getpostman.com/apps) pour générer le reste appels ou vous pouvez utiliser n’importe quel environnement de développement d’API que vous préférez.


## <a name="best-practices-for-geocoding"></a>Meilleures pratiques pour le géocodage

Lorsque vous recherchez une adresse complète ou partielle à l’aide du Service de recherche Azure Maps, il prend votre terme de recherche et retourne les coordonnées de longitude et latitude de l’adresse. Ce processus est appelé à géocodage. La possibilité de géocoder dans un pays dépend de la couverture de données de route et de la précision du service de géocodage.

Consultez [couverture du géocodage](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) pour en savoir plus sur les fonctionnalités de géocodage Azure Maps par pays/région.

### <a name="limit-search-results"></a>Limiter les résultats de recherche

   Dans cette section, vous allez apprendre à utiliser les API de recherche Azure Maps pour limiter les résultats de la recherche. 

   > [!Note]
   > Recherche pas toutes les API entièrement prennent en charge les paramètres répertoriés ci-dessous

   **Résultats de la recherche de géo-décalage**

   Dans l’ordre de géo-écart vos résultats dans le domaine approprié pour votre utilisateur, vous devez toujours ajouter le nombre maximal possible détaillé entrée de l’emplacement. Pour restreindre les résultats de recherche, vous pouvez envisager les types d’entrée suivants :

   1. Définir le `countrySet` paramètre, par exemple « US, FR ». Le comportement de recherche par défaut consiste à rechercher le monde entier, potentiellement retournant les résultats inutiles. Si votre requête n’inclut pas `countrySet` paramètre, la recherche peut retourner des résultats incorrects. Par exemple, rechercher une ville nommée **Bellevue** renvoie des résultats à partir des États-Unis et de France, dans la mesure où il existe des villes nommées **Bellevue** en France et aux États-Unis d’Amérique.

   2. Vous pouvez utiliser la `btmRight` et `topleft` zone de paramètres pour définir le cadre englobant pour limiter la recherche à une zone spécifique de la carte.

   3. Pour influer sur la zone de pertinence pour les résultats, vous pouvez définir le `lat`et `lon` coordonner les paramètres et de définir le rayon de la zone de recherche à l’aide de le `radius` paramètre.


   **Paramètres de recherche floue**

   1. Le `minFuzzyLevel` et `maxFuzzyLevel`, aide les correspondances pertinentes même lorsque les paramètres de requête ne correspondent pas exactement aux informations désirées. Par défaut, la plupart des requêtes de recherche `minFuzzyLevel=1` et `maxFuzzyLevel=2` pour obtenir des performances et de réduire les résultats inhabituels. Prenons l’exemple d’un terme de recherche « restrant », il est mis en correspondance avec « restaurant » lorsque le `maxFuzzyLevel` est définie à 2. Les niveaux floue par défaut peuvent être remplacées en fonction des besoins de demande. 

   2. Vous pouvez également spécifier le jeu exact des types de résultats à retourner à l’aide de le `idxSet` paramètre. Pour cela vous pouvez soumettre la virgule comme séparateur de liste d’index, l’ordre de l’élément n’a pas d’importance. Les index pris en charge sont les suivantes :

       * `Addr` - **Plages d’adresses**: Pour certaines rues, il existe des points d’adresse sont interpolées de début et de fin de la rue ; Ces points sont représentées sous forme de plages d’adresses.
       * `Geo` - **Zones géographiques**: Zones d’une carte qui représentent une division administrative d’un land, autrement dit, pays, ville.
       * `PAD` - **Adresse du point**:  Points sur une carte où une adresse spécifique avec un nom de la rue et un nombre figurent dans un index, par exemple, 2501 de récupération d’urgence Soquel. Il est le plus haut niveau de précision disponible pour les adresses.  
       * `POI` - **Points d’intérêt**: Points sur une carte qui méritent une attention particulière et peuvent être intéressant.  [Obtenir l’adresse de la recherche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ne retourne pas des points d’intérêts.  
       * `Str` - **Streets**: Représentation sous forme de rues sur la carte.
       * `XStr` - **Cross/intersections Streets**:  Représentation sous forme de jonctions ; place l’intersection de deux streets.


       **Exemples d’utilisation**:

       * idxSet = POI (Points d’intérêt recherche uniquement) 

       * idxSet = remplir, Addr (recherche des adresses uniquement, panneau = Point Address, Addr = plage d’adresses)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Inverser le filtre de type d’entité Géocode et geography

Lorsque vous effectuez une recherche de Géocode inverse avec [API recherche d’adresse inverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), le service a la possibilité de retourner les polygones pour les tâches administratives. En ajoutant le paramètre `entityType` de la demande, vous pouvez affiner la recherche de types d’entité géographique spécifiée. La réponse résultante contient l’ID de zone géographique, ainsi que le type d’entité mis en correspondance. Si vous fournissez plusieurs entités, point de terminaison renvoie le **plus petite entité disponible**. Retourné ID de géométrie peut être utilisé pour obtenir la géométrie de la zone géographique via [service d’obtenir le polygone](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Exemple de demande :**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Réponse :**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Langue des résultats de recherche

Le `language` paramètre vous permet de définir dans la recherche de langage qui les résultats doivent être retournés. Si la langue n’est pas définie dans la demande, service de recherche automatiquement par défaut est le langage le plus répandu dans le pays/région. En outre, lorsque les données dans la langue spécifiée ne sont pas disponibles, la langue par défaut est utilisée. Consultez [langues prises en charge](https://docs.microsoft.com/azure/azure-maps/supported-languages) pour obtenir la liste des langues prises en charge en ce qui concerne les services Azure Maps par pays/région.


### <a name="predictive-mode-auto-suggest"></a>Mode prédictif (suggestion automatique)

Pour rechercher des correspondances plus pour les requêtes partielles, `typeHead` paramètre doit être défini sur ' true '. La requête sera interprétée comme une entrée partielle et la recherche bascule en mode prédictif. Sinon, le service suppose que toutes les informations pertinentes n’a été transmises.

Dans l’exemple de requête ci-dessous, vous pouvez voir que le service de l’adresse de recherche est interrogé pour « Microsoft » avec le `typehead` paramètre défini sur **true**. Si vous observez la réponse, vous pouvez voir que le service de recherche interprété la requête en tant que requête partielle et réponse contient les résultats de requête suggérées automatiquement.

**Exemple de requête :**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Réponse :**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI d’encodage pour gérer les caractères spéciaux 

Pour trouver entre les adresses postales, autrement dit, « 1er Avenue & Union Street, Seattle », le caractère spécial '&' doit être encodée avant d’envoyer la demande. Nous vous recommandons d’encodage des données de caractères dans un URI, où tous les caractères sont encodés à l’aide d’un caractère « % » et une valeur hexadécimale à deux caractères correspondant à leur caractère UTF-8.

**Exemples d’utilisation**:

Obtenir l’adresse de la recherche :

```
query=1st Avenue & E 111th St, New York
```

 doit être codée comme suit :

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Voici les différentes méthodes à utiliser pour des langues différentes : 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/ VB :
```C#
Uri.EscapeDataString(query)
```

Java :
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python :
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++ :
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP :
```PHP
urlencode(query)
```

Ruby :
```Ruby
CGI::escape(query) 
```

SWIFT :
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go :
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Meilleures pratiques pour la recherche de point d’intérêt

Points d’intérêt (POI) recherche vous permet de demander les résultats de point d’intérêt par nom, par exemple, les professionnels de recherche par nom. Nous vous recommandons vivement d’utiliser le `countrySet` paramètre pour spécifier des pays où votre application doit couvrir, car le comportement par défaut sera pour rechercher le monde entier, potentiellement retournant les résultats inutiles et/ou entraîne des temps de recherche.

### <a name="brand-search"></a>Recherche de marque

Pour améliorer la pertinence des résultats et les informations contenues dans la réponse, la réponse de recherche de Point d’intérêt (POI) inclut les informations de marque qui peuvent être utilisées plus à analyser la réponse.

Nous allons effectuer un [recherche de catégorie POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) demande pour stations-services près campus de Microsoft (Redmond, WA). Si vous observez la réponse, vous pouvez voir des informations sur les marques pour chaque point d’intérêt retourné.

**Exemple de requête :**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Réponse :**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Recherche de l’aéroport

POI recherche prend en charge la recherche des aéroports en utilisant les codes d’aéroport officiels. Par exemple, **SEA** (l’aéroport International de Seattle-Tacoma). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Recherche de proximité

Pour récupérer uniquement les résultats de point d’intérêt autour d’un emplacement spécifique, le [à proximité de l’API recherche](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) peut être le bon choix. Ce point de terminaison renvoie uniquement les résultats de point d’intérêt et ne prend pas de paramètre de requête de recherche. Pour limiter les résultats, il est recommandé de définir le rayon.

## <a name="understanding-the-responses"></a>Comprendre les réponses

Nous allons effectuer une requête de recherche d’adresse pour le Azure Maps [service de recherche](https://docs.microsoft.com/rest/api/maps/search) pour une adresse à Seattle. Si vous examinez attentivement l’URL de requête ci-dessous, nous avons défini le `countrySet` paramètre **US** pour rechercher l’adresse dans les États-Unis d’Amérique.

**Exemple de requête :**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Plus intéressons-nous à la structure de réponse ci-dessous. Les types de résultat les objets de résultats dans la réponse sont différents. Si vous observez attentivement que vous constatez que nous avons trois différents types d’objets de résultat, qui sont « Adresse de Point », « Street » et « Cross Street ». Notez que la recherche adresse ne retourne pas de points d’intérêts. Le `Score` paramètre pour chaque objet de réponse indique le score de correspondance relatif à un grand nombre d’autres objets dans la même réponse. Consultez [obtenir une adresse de recherche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) pour en savoir plus sur les paramètres d’objet de réponse.

**Types pris en charge du résultat :**

* **Adresse de point :** Points sur une carte avec une adresse spécifique avec un nom de la rue et un nombre. Le niveau le plus élevé de précision disponible pour les adresses. 

* **Plage d’adresses :**  Pour certaines rues, il existe des points d’adresse sont interpolées de début et de fin de la rue ; Ces points sont représentées sous forme de plages d’adresses. 

* **Géographie :** Zones d’une carte qui représentent une division administrative d’un land, autrement dit, pays, ville. 

* **POI - (Points d’intérêt) :** Points sur une carte qui méritent une attention particulière et peuvent être intéressant.

* **Rue :** Représentation sous forme de rues sur la carte. Les adresses sont résolues à la coordonnée de latitude/longitude de la rue qui contient l’adresse. Le numéro de résidence ne peut pas être traité. 

* **Intersection :** Intersections. Représentations sous forme de jonctions ; place l’intersection de deux streets.

**Réponse :**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Géométrie

Lorsque le type de réponse est **Geometry**, il peut inclure l’ID de géométrie qui est retourné dans le **sources de données** objet sous « geometry » et « id ». Par exemple, [service d’obtenir le polygone](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) vous permet de demander les données de géométrie au format GeoJSON, par exemple un plan de ville ou un aéroport pour un jeu d’entités. Vous pouvez utiliser ces données de limites pour [gardiennage virtuel](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou [recherche des points d’intérêts à l’intérieur de la géométrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Rechercher l’adresse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou [recherche floue](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) réponses de l’API peuvent inclure le **ID de géométrie** qui est retourné dans l’objet de sources de données sous « geometry » et « id ».


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus [comment générer des demandes de service Azure Maps recherche](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Explorez les mappages Azure [rechercher la documentation de l’API service](https://docs.microsoft.com/rest/api/maps/search). 