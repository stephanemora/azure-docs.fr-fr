---
title: Recherche efficace à l’aide du service Recherche Azure Maps | Microsoft Docs
description: Découvrez les meilleures pratiques en matière de recherche à l’aide du service Recherche Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6a51d764b8e42419bc331e3d4731ef5c5f511f91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408714"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Meilleures pratiques en matière d’utilisation du service Recherche Azure Maps

Le [service Recherche](https://docs.microsoft.com/rest/api/maps/search) Azure Maps comporte des API offrant diverses fonctionnalités, depuis la recherche d’adresses jusqu’à la recherche de données de point d’intérêt (POI) autour d’un emplacement spécifique. Cet article vous présente les meilleures pratiques concernant l’appel des données par le biais du service Recherche Azure Maps. Vous apprendrez à :

* Générer des requêtes destinées à renvoyer les correspondances pertinentes
* Limiter les résultats de la recherche
* Identifier les différences entre les divers types de résultats
* Lire la structure de réponse des recherches d’adresse


## <a name="prerequisites"></a>Conditions préalables requises

Pour appeler les API du service Maps, vous avez besoin d’un compte et d’une clé Maps. Pour plus d’informations sur la création d’un compte, suivez les instructions fournies dans [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) et les étapes décrites dans [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) afin de récupérer une clé primaire d’abonnement pour votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

> [!Tip]
> Pour interroger le service de recherche, vous pouvez utiliser [l’application Postman](https://www.getpostman.com/apps) afin de générer des appels REST, ou vous avez la possibilité de recourir à l’environnement de développement d’API que vous préférez.


## <a name="best-practices-for-geocoding"></a>Meilleures pratiques en matière de géocodage

Lorsque vous recherchez une adresse complète ou partielle à l’aide du service Recherche Azure Maps, ce dernier considère votre terme de recherche, puis renvoie les coordonnées de longitude et de latitude de l’adresse. Ce processus est désigné sous le terme de « géocodage ». La possibilité de géocoder dans un pays dépend de la couverture de données de route et de la précision du service de géocodage.

Pour plus d’informations sur les fonctionnalités de géocodage Azure Maps par pays/région, consultez l’article concernant la [couverture du géocodage](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Limiter les résultats de la recherche

   Dans cette section, vous allez apprendre à limiter les résultats de la recherche à l’aide des API de recherche Azure Maps. 

   > [!Note]
   > Certaines API de recherche ne prennent pas complètement en charge les paramètres répertoriés ci-dessous.

   **Affiner géographiquement les résultats de la recherche**

   Pour affiner vos résultats en fonction de la zone géographique de votre utilisateur, ajoutez systématiquement le plus grand nombre possible d’entrées de localisation détaillées. Pour restreindre les résultats de la recherche, vous pouvez ajouter les types d’entrées suivants :

   1. Définissez le paramètre `countrySet`, par exemple « US,FR ». Le comportement de recherche par défaut consiste à faire porter la recherche sur le monde entier, et risque ainsi de renvoyer des résultats superflus. Si votre requête n’inclut pas le paramètre `countrySet`, la recherche peut renvoyer des résultats incorrects. Par exemple, la recherche d’une ville appelée **Bellevue** renvoie des résultats situés aux États-Unis aussi bien qu’en France, car ces deux pays comprennent des villes du nom de **Bellevue**.

   2. Vous pouvez utiliser les paramètres `btmRight` et `topleft` pour définir le cadre englobant permettant de limiter la recherche à une zone spécifique de la carte.

   3. Pour optimiser la pertinence des résultats, vous pouvez définir les paramètres de coordonnée `lat` et `lon` et définir le rayon de la zone de recherche à l’aide du paramètre `radius`.


   **Paramètres de recherche approximative**

   1. Les paramètres `minFuzzyLevel` et `maxFuzzyLevel` favorisent le renvoi de correspondances pertinentes, même lorsque les paramètres de requête ne correspondent pas exactement aux informations souhaitées. La plupart des requêtes de recherche utilisent par défaut les valeurs `minFuzzyLevel=1` et `maxFuzzyLevel=2` pour optimiser les performances et réduire les résultats inhabituels. Considérons l’exemple du terme de recherche « restrant ». Il est mis en correspondance avec le terme « restaurant » lorsque le paramètre `maxFuzzyLevel` est défini sur 2. Vous pouvez écraser les niveaux d’approximation par défaut en fonction de vos besoins de requête. 

   2. Vous pouvez également spécifier le jeu exact des types de résultats à renvoyer à l’aide du paramètre `idxSet`. Pour cela, vous pouvez soumettre une liste d’index séparés par une virgule, dont l’ordre importe peu. Les index pris en charge sont les suivants :

       * `Addr` - **Plages d’adresses** : dans le cas de certaines rues, des points d’adresse sont interpolés entre le début et la fin de la rue ; ces points sont représentés sous la forme de plages d’adresses.
       * `Geo` - **Zones géographiques** : zones d’une carte qui représentent une division administrative d’un territoire, telle qu’un pays, une région ou une ville.
       * `PAD` - **Adresse exacte** :  points sur une carte à l’emplacement desquels une adresse spécifique comportant un nom et un numéro de rue peut être trouvée dans un index. Exemple : 25 rue des Cerisiers. Il s’agit du plus haut niveau de précision disponible pour les adresses.  
       * `POI` - **Points d’intérêt** : points sur une carte qui méritent une attention particulière et peuvent être intéressants.  [L’API d’obtention de l’adresse recherchée](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ne renvoie pas les points d’intérêt.  
       * `Str` - **Rues** : représentation des rues sur la carte.
       * `XStr` - **Croisements/intersections** :  représentation des carrefours, autrement dit des points d’intersection entre deux rues.


       **Exemples d’utilisation** :

       * idxSet=POI (recherche uniquement les points d’intérêt) 

       * idxSet=PAD,Addr (recherche uniquement les adresses ; PAD = adresse exacte, Addr = plage d’adresses)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Géocode inversé et filtre de type d’entité géographique

Lorsque vous effectuez une recherche de géocode inversé à l’aide de [l’API de recherche d’adresse inversée](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), le service est en mesure de renvoyer des polygones correspondant aux zones administratives. La spécification du paramètre `entityType` dans la requête vous permet d’affiner la recherche pour qu’elle renvoie les types d’entités géographiques spécifiés. La réponse obtenue contient l’ID de géographie, ainsi que le type d’entité mis en correspondance. Si vous fournissez plusieurs entités, le point de terminaison renvoie la **plus petite entité disponible**. L’ID de géométrie renvoyé permet d’obtenir la géométrie de cette géographie par le biais du [service d’obtention du polygone](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Exemple de requête :**

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

### <a name="search-results-language"></a>Langue des résultats de la recherche

Le paramètre `language` vous permet de définir la langue dans laquelle les résultats de la recherche doivent être renvoyés. Si la langue n’est pas définie dans la requête, le service de recherche utilise automatiquement par défaut la langue la plus répandue dans le pays ou la région concernés. En outre, si les données ne sont pas disponibles dans la langue spécifiée, le service applique la langue par défaut. Pour obtenir la liste des langues par pays/région qui sont gérées par les services Azure Maps, consultez l’article concernant les [langues prises en charge](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="predictive-mode-auto-suggest"></a>Mode prédictif (suggestion automatique)

Pour rechercher des correspondances supplémentaires pour les requêtes partielles, définissez le paramètre `typeahead` sur la valeur « true ». La requête est alors interprétée en tant qu’entrée partielle, et la recherche passe en mode prédictif. Dans le cas contraire, le service suppose que toutes les informations pertinentes lui ont été transmises.

Dans l’exemple de requête ci-après, le terme de recherche « Microso » est soumis au service de recherche d’adresse, et le paramètre `typeahead` est défini sur **true**. Si vous examinez la réponse, vous pouvez constater que le service de recherche a interprété la requête comme une requête partielle, et que la réponse contient des résultats correspondant à des suggestions automatiques.

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


### <a name="uri-encoding-to-handle-special-characters"></a>Encodage d’URI pour gérer les caractères spéciaux 

Pour rechercher les adresses situées à un croisement, par exemple « rue de Rivoli & rue du Louvre, Paris », vous devez encoder le caractère spécial « & » avant d’envoyer la requête. Nous vous recommandons d’encoder les données de caractère dans un URI, où tous les caractères sont encodés à l’aide d’un caractère « % » et d’une valeur hexadécimale à deux caractères correspondant à leur caractère UTF-8.

**Exemples d’utilisation** :

La requête d’obtention de l’adresse recherchée suivante :

```
query=1st Avenue & E 111th St, New York
```

 doit être encodée comme suit :

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Voici les diverses méthodes à utiliser pour les différents langages : 

JavaScript/TypeScript :
```Javascript
encodeURIComponent(query)
```

C#/VB :
```csharp
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

Ruby :
```Ruby
CGI::escape(query) 
```

Swift :
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go :
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Meilleures pratiques en matière de recherche de points d’intérêt

La recherche de points d’intérêt vous permet de demander les résultats de point d’intérêt par nom. Par exemple, vous pouvez rechercher une entreprise par son nom. Nous vous recommandons vivement d’utiliser le paramètre `countrySet`pour spécifier les pays que votre application doit couvrir ; en effet, le comportement par défaut fait porter la recherche sur le monde entier, ce qui peut engendrer des résultats superflus et/ou des temps de recherche plus longs.

### <a name="brand-search"></a>Recherche de marque

Pour améliorer la pertinence des résultats et les informations figurant dans la réponse, la réponse d’une recherche de points d’intérêt inclut les informations de marque qui permettent par la suite d’analyser la réponse.

Exécutons une requête de [recherche de catégorie de point d’intérêt](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) portant sur les stations-service proches du campus de Microsoft (Redmond, Washington). Lorsque vous examinez la réponse, vous pouvez constater que les informations de marque sont renvoyées pour chaque point d’intérêt.

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


### <a name="airport-search"></a>Recherche d’aéroports

La recherche de points d’intérêt prend en charge la recherche d’aéroports à l’aide des codes d’aéroport officiels. Par exemple, **CDG** (aéroport international de Paris-Charles-de-Gaulle). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Recherche à proximité

Pour récupérer uniquement les résultats de point d’intérêt situés dans les environs d’un emplacement spécifique, il peut être judicieux d’utiliser [l’API de recherche à proximité](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). Ce point de terminaison renvoie uniquement les résultats de point d’intérêt et ne prend pas en compte un paramètre de requête de recherche. Pour restreindre les résultats, il est recommandé de définir le rayon.

## <a name="understanding-the-responses"></a>Compréhension des réponses

Exécutons une requête de recherche d’adresse destinée au [service de recherche](https://docs.microsoft.com/rest/api/maps/search) Azure Maps et portant sur une adresse située à Seattle. Si vous examinez attentivement l’URL de requête ci-dessous, nous avons défini le paramètre `countrySet` sur la valeur **US** afin de rechercher l’adresse spécifiée aux États-Unis.

**Exemple de requête :**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Étudions de plus près la structure de réponse ci-après. Les objets de résultat figurant dans la réponse présentent différents types. Si vous les observez attentivement, vous pouvez constater que les objets de résultat sont de trois types : « Adresse exacte », « Rue » et « Croisement ». Notez que la recherche d’adresse ne renvoie pas les points d’intérêt. Le paramètre `Score` de chaque objet de réponse indique le score de correspondance de cet objet par rapport aux autres objets qui figurent dans la même réponse. Pour plus d’informations sur les paramètres d’objet de réponse, consultez l’article [Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) (Obtention de l’adresse recherchée).

**Types de résultats pris en charge :**

* **Adresse exacte :** points sur une carte correspondant à une adresse spécifique comportant un nom et un numéro de rue. Il s’agit du plus haut niveau de précision disponible pour les adresses. 

* **Plage d’adresses :**  dans le cas de certaines rues, des points d’adresse sont interpolés entre le début et la fin de la rue ; ces points sont représentés sous la forme de plages d’adresses. 

* **Zone géographique :** zones d’une carte qui représentent une division administrative d’un territoire, telle qu’un pays, une région ou une ville. 

* **Points d’intérêt :** points sur une carte qui méritent une attention particulière et peuvent être intéressants.

* **Rue :** représentation des rues sur la carte. Les adresses sont converties en coordonnées de latitude/longitude de la rue correspondante. Le numéro de résidence ne peut pas être traité. 

* **Croisement :** intersections. Représentation des carrefours, autrement dit des points d’intersection entre deux rues.

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

Lorsque la réponse présente le type **Géométrie**, elle peut inclure l’ID de géométrie renvoyé dans l’objet **dataSources** sous « geometry » et « id ». Par exemple, le [service d’obtention de polygone](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) vous permet de demander des données de géométrie au format GeoJSON, telles que le schéma d’une ville ou d’un aéroport pour un ensemble d’entités. Vous pouvez utiliser ces données de limite pour les fonctionnalités de [geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou de [recherche des points d’intérêt à l’intérieur de la géométrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Les réponses des API de [recherche d’adresse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou de [recherche approximative](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) peuvent inclure **l’ID de géométrie** renvoyé dans l’objet dataSources sous « geometry » et « id ».


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Étapes suivantes

* Apprenez à [générer des requêtes destinées au service Recherche Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Explorez la [documentation sur les API du service Recherche](https://docs.microsoft.com/rest/api/maps/search) Azure Maps. 
