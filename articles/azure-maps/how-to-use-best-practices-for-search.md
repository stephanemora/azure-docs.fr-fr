---
title: Rechercher efficacement à l’aide du service Recherche Azure Maps | Microsoft Azure Maps
description: Découvrez comment appliquer les meilleures pratiques pour le service Recherche à l’aide de Microsoft Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845758"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Meilleures pratiques en matière d’utilisation du service Recherche Azure Maps

Le [service Recherche](https://docs.microsoft.com/rest/api/maps/search) d’Azure Maps comprend des API avec diverses fonctionnalités. Par exemple, l’API de recherche d’adresses est utilisée pour rechercher un point d’intérêt (POI) ou des données autour d’un emplacement spécifique. Cet article présente des pratiques de son à appliquer lors de l’appel de données à partir des services de recherche Azure Maps. Vous apprendrez à :

* Générer des requêtes destinées à renvoyer les correspondances pertinentes
* Limiter les résultats de la recherche
* Identifier les différences entre les divers types de résultats
* Lire la structure de réponse des recherches d’adresse


## <a name="prerequisites"></a>Conditions préalables requises

Pour appeler les API du service Maps, vous avez besoin d’un compte Azure Maps et d’une clé. Au besoin, suivez les instructions des sections [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) et [Obtenir une clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account). Pour plus d’informations sur l’authentification dans Azure Maps, voir [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

> [!Tip]
> Pour interroger le service de recherche, vous pouvez utiliser [l’application Postman](https://www.getpostman.com/apps) afin de générer des appels REST, ou vous avez la possibilité de recourir à l’environnement de développement d’API que vous préférez.


## <a name="best-practices-for-geocoding-address-search"></a>Meilleures pratiques en matière de géocodage (recherche d’adresse)

Lorsque vous recherchez une adresse complète ou partielle à l’aide du service Recherche Azure Maps, L’API lit les mots clés de votre requête de recherche, puis renvoie les coordonnées de longitude et de latitude de l’adresse. Ce processus est désigné sous le terme de « géocodage ». La possibilité de géocoder dans un pays dépend de la couverture de données de route et de la précision du service de géocodage.

Pour plus d’informations sur les fonctionnalités de géocodage Azure Maps par pays/région, consultez l’article concernant la [couverture du géocodage](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Limiter les résultats de la recherche

 L’API de recherche Azure Maps peut vous aider à limiter les résultats de recherche de manière appropriée, afin que vous puissiez afficher des données pertinentes à vos utilisateurs.

   > [!Note]
   > Certains paramètres pris en charge des API de recherche ne sont pas répertoriés ci-dessous

   **Affiner géographiquement les résultats de la recherche**

   Pour affiner vos résultats en fonction de la zone géographique de votre utilisateur, ajoutez systématiquement le plus grand nombre possible d’entrées de localisation détaillées. Pour restreindre les résultats de la recherche, vous pouvez ajouter les types d’entrées suivants :

   1. Définissez le paramètre `countrySet`, par exemple « US,FR ». Le comportement de recherche par défaut consiste à faire porter la recherche sur le monde entier, et risque ainsi de renvoyer des résultats superflus. Si votre requête n’inclut pas le paramètre `countrySet`, la recherche peut renvoyer des résultats incorrects. Par exemple, la recherche d’une ville nommée **Bellevue** renvoie des résultats pour les États-Unis et la France, car il existe des villes du nom de **Bellevue** dans ces deux pays.

   2. Vous pouvez utiliser les paramètres `btmRight` et `topleft` pour définir le cadre englobant permettant de restreindre la recherche à une zone spécifique sur la carte.

   3. Pour optimiser la pertinence des résultats, vous pouvez définir les paramètres de coordonnée `lat` et `lon` et définir le rayon de la zone de recherche à l’aide du paramètre `radius`.


   **Paramètres de recherche approximative**
   
  L’[API de recherche approximative](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) Azure Maps est le service recommandé à utiliser lorsque vous ne connaissez pas les entrées utilisateur pour une requête de recherche. L’API combine la recherche de points d’intérêt (POI) et le géocodage dans une *recherche canonique à ligne unique*.

   1. Les paramètres `minFuzzyLevel` et `maxFuzzyLevel` permettent de renvoyer des correspondances pertinentes, même lorsque les paramètres de requête ne correspondent pas exactement aux informations souhaitées. Pour améliorer les performances et réduire les résultats inhabituels, définissez les requêtes de recherche par défaut sur `minFuzzyLevel=1` et `maxFuzzyLevel=2`. Considérons l’exemple du terme de recherche « restrant ». Il est mis en correspondance avec le terme « restaurant » lorsque le paramètre `maxFuzzyLevel` est défini sur 2. Vous pouvez modifier les niveaux d’approximation par défaut en fonction de vos besoins.  

   2. Vous pouvez également classer par ordre de priorité le jeu exact des types de résultats à renvoyer à l’aide du paramètre `idxSet`. Pour cela, vous pouvez soumettre une liste d’index séparés par une virgule, dont l’ordre importe peu. Les index suivants sont pris en charge :

       * `Addr` - **Plages d’adresses** : Pour certaines rues, il y a des points d’adresse qui sont interpolés du début et à la fin de la rue. Ces points sont représentés sous forme de plages d’adresses.
       * `Geo` - **Zones géographiques** : zones d’une carte qui représentent une division administrative d’un territoire, telle qu’un pays, une région ou une ville.
       * `PAD` - **Adresse exacte** :  points sur une carte à l’emplacement desquels une adresse spécifique comportant un nom et un numéro de rue peut être trouvée dans un index. Exemple : 25 rue des Cerisiers. Cette valeur idxSet correspond au niveau de précision le plus haut disponible pour les adresses.  
       * `POI` - **Points d’intérêt** : points sur une carte qui méritent une attention particulière et peuvent être intéressants.  [L’API d’obtention de l’adresse recherchée](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ne renvoie pas les points d’intérêt.  
       * `Str` - **Rues** : représentation des rues sur la carte.
       * `XStr` - **Croisements/intersections** :  représentation des carrefours, autrement dit des points d’intersection entre deux rues.


       **Exemples d’utilisation** :

       * idxSet=POI (recherche uniquement les points d’intérêt) 

       * idxSet=PAD,Addr (recherche uniquement les adresses ; PAD = adresse exacte, Addr = plage d’adresses)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Géocode inversé et filtre de type d’entité géographique

Lorsque vous effectuez une recherche de géocode inversé à l’aide de [l’API de recherche d’adresse inversée](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), le service est en mesure de renvoyer des polygones correspondant aux zones administratives. La spécification du paramètre `entityType` dans la requête vous permet d’affiner la recherche pour qu’elle renvoie les types d’entités géographiques spécifiés. La réponse obtenue contient l’ID de géographie et le type d’entité mis en correspondance. Si vous fournissez plusieurs entités, le point de terminaison renvoie la **plus petite entité disponible**. L’ID de géométrie renvoyé permet d’obtenir la géométrie de cette géographie par le biais du [service d’obtention du polygone](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Exemple de requête :**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Réponse :**

```JSON
{
    "summary": {
        "queryTime": 14,
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

Le paramètre `language` vous permet de sélectionner la langue des résultats retournés par l’API. Si la langue n’est pas définie dans la requête, le service de recherche utilise automatiquement par défaut la langue la plus répandue dans le pays ou la région concernés. En outre, si les données ne sont pas disponibles dans la langue spécifiée, le service applique la langue par défaut. Pour obtenir la liste par pays/région des langues prises en charge par les services Azure Maps, voir [Langues prises en charge](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="predictive-mode-autosuggest"></a>Mode prédictif (suggestion automatique)

Pour rechercher des correspondances supplémentaires pour les requêtes partielles, définissez le paramètre `typeahead` sur la valeur « true ». La requête est alors interprétée en tant qu’entrée partielle, et la recherche passe en mode prédictif. Dans le cas contraire, le service suppose que toutes les informations pertinentes lui ont été transmises.

Dans l’exemple de requête ci-après, le terme de recherche « Microso » est soumis au service de recherche d’adresse, et le paramètre `typeahead` est défini sur **true**. En examinant la réponse, vous pouvez voir que le service de recherche a interprété la requête comme une requête partielle. La réponse contient les résultats de la requête générée par suggestion automatique.

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
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Encodage d’URI pour gérer les caractères spéciaux 

Pour rechercher des adresses de croisement, vous devez encoder l’URI pour gérer des caractères spéciaux dans l’adresse. Prenons cet exemple d’adresse : « 1st Avenue & Union Street, Seattle ». Le caractère spécial « & » doit être encodé avant l’envoi de la demande. Nous vous recommandons d’encoder les données de caractère dans un URI, où tous les caractères sont encodés à l’aide d’un caractère « % » et d’une valeur hexadécimale à deux caractères correspondant à leur caractère UTF-8.

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

Vous pouvez également envoyer une liste de noms de marques séparée par une virgule dans la requête. Vous pouvez utiliser la liste pour limiter les résultats à des marques spécifiques à l’aide du paramètre `brandSet`. L’ordre des éléments importe peu. Lorsque plusieurs marques sont fournies, seuls les résultats répertoriés dans au moins une des listes fournies sont retournés.

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
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
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
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Géométrie

Lorsque la réponse présente le type **Géométrie**, elle peut inclure l’ID de géométrie renvoyé dans l’objet **dataSources** sous « geometry » et « id ». Par exemple, la commande [Get Polygon service](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) vous permet de demander les données géométriques au format GeoJSON. Par exemple, un plan de ville ou d’aéroport pour un ensemble d’entités. Vous pouvez utiliser ces données de limite pour les fonctionnalités de [geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou de [recherche des points d’intérêt à l’intérieur de la géométrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


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
