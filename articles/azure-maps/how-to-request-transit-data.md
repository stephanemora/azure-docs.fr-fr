---
title: Demander des données de transport | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à demander des données de transport public à l’aide de Microsoft Azure Maps Mobility Service.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 17fbc78b34237c6f5e1e688a88c68bb0a321884f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209866"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Demander des données de transport public à l’aide d’Azure Maps Mobility Service 

Cet article explique comment utiliser Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) pour demander des données sur les transports publics. Les données relatives aux transports comprennent les arrêts, les itinéraires et les estimations de temps de trajet.

Dans cet article, vous allez apprendre à :

* Obtenir un ID de zone urbaine via l’[API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro)
* Demandez des arrêts de transport proches, en utilisant le service [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit).
* Utiliser l'[API Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute) pour planifier un trajet à l'aide des transports publics.
* Obtenez la géométrie routière et les horaires détaillées du trajet via l’[API Get Transit Itinerary](https://aka.ms/https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Prérequis

Vous devez d'abord disposer d'un compte Azure Maps et d'une clé d'abonnement pour appeler les API de transports publics Azure Maps. Pour plus d'informations sur la création d'un compte Azure Maps, suivez les instructions de la section [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps). Suivez les étapes de la section [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) afin de vous procurer la clé primaire de votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, voir [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).


Cet article utilise [l’application Postman](https://www.getpostman.com/apps) pour générer des appels REST. Vous pouvez utiliser l’environnement de développement d’API que vous préférez.


## <a name="get-a-metro-area-id"></a>Obtenir un ID de zone urbaine

Pour demander des informations sur les transports disponibles dans une zone urbaine particulière, vous devez disposer de l'`metroId` de cette zone. L'[API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro) vous permet d'interroger des zones urbaines dans lesquelles Azure Maps Mobility Service est disponible. La réponse inclut des informations telles que `metroId`, `metroName` et la représentation de la géométrie routière de la zone urbaine au format GeoJSON.

Nous allons effectuer une demande pour obtenir l'ID de la zone urbaine Seattle-Tacoma. Pour demander d’lD d’une zone urbaine, procédez comme suit :

1. Ouvrez l'application Postman, puis créez une collection destinée au stockage des demandes. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Collection**.  Nommez la collection puis sélectionnez le bouton **Create** (Créer).

2. Pour créer la demande, sélectionnez **New** à nouveau. Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande). Entrez un **Request name** (Nom de demande) pour la demande. Sélectionnez la collection que vous avez créée à l'étape précédente comme emplacement d'enregistrement de la demande. Sélectionnez ensuite **Enregistrer**.
    
    ![Créer une demande dans Postman](./media/how-to-request-transit-data/postman-new.png)

3. Sélectionnez la méthode HTTP **GET** sous l'onglet Builder (Générateur), puis entrez l'URL suivante pour créer une requête GET. Remplacez `{subscription-key}` par votre clé primaire Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Après une demande réussie, vous recevrez la réponse suivante :

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. Copiez le `metroId` car nous en aurons besoin plus tard.

## <a name="request-nearby-transit-stops"></a>Demander des arrêts de transport proches

Le service Azure Maps [Get Near Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) vous permet de rechercher des objets de transport.  L'API renvoie les détails de l'objet de transport, tels que les arrêts de transports publics et les vélos partagés autour d'un emplacement donné. Nous allons ensuite demander au service de rechercher les arrêts de transports publics disponibles dans un rayon de 300 mètres autour de la zone indiquée. Dans la demande, nous devons inclure le `metroId` récupéré plus tôt.

Pour effectuer une demande au service [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit), procédez comme suit :

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Get Nearby Stops**.

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête pour le point de terminaison de votre API puis cliquez sur **Send** (Envoyer).

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Après une demande réussie, la structure de la réponse doit ressembler à ce qui suit :

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

Si vous observez attentivement la structure de la réponse, vous constaterez qu'elle contient les paramètres de chaque objet de transport. Chaque objet de transport comporte des paramètres tels que `id`, `type`, `stopName`, `mainTransitType`, `mainAgencyName`, ainsi que la position de l'objet, sous forme de coordonnées.

Par souci de simplicité, dans la section suivante, nous utiliserons un `id` d'arrêt de bus comme point de départ de notre itinéraire.  


## <a name="request-a-transit-route"></a>Demander un itinéraire de transport

L'API Azure Maps [Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute) permet de planifier des trajets. Il renvoie les meilleures options d'itinéraire possibles entre un point de départ et une destination. Le service propose différents types de modes de déplacement, comme la marche, le vélo et les transports publics. Il nous faut ensuite chercher un itinéraire entre l'arrêt de bus le plus proche et la Space Needle de Seattle.

### <a name="get-location-coordinates-for-destination"></a>Obtenir les coordonnées de la destination

Pour obtenir les coordonnées de la Space Needle, nous utilisons le service [Fuzzy Search Service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) d'Azure Maps.

Pour effectuer une demande au service Fuzzy Search Service, procédez comme suit :

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Get location coordinates**.

2.  Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête puis cliquez sur **Send** (Envoyer).
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Si vous examinez attentivement la réponse, vous constaterez que plusieurs emplacements sont proposés dans les résultats de la recherche Space Needle. Chaque résultat contient les coordonnées de l'emplacement sous la **position**. Copiez les valeurs `lat` et `lon` sous la **position** du premier résultat.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>Demander un itinéraire

Pour demander un itinéraire, procédez comme suit :

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Get Route info**.

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête pour le point de terminaison de votre API puis cliquez sur **Send** (Envoyer).

    Nous allons demander des itinéraires de transport en bus en spécifiant les paramètres `modeType` et `transitType`. La requête URL contient les emplacements obtenus dans les sections précédentes. Pour le type `originType`, nous disposons désormais d'un ID **stopId**. Et pour le type `destionationType`, nous disposons de la **position**.

    Consultez la [liste des paramètres URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) que vous pouvez utiliser dans votre requête avec l’[API Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Après une demande réussie, la structure de la réponse doit ressembler à ce qui suit :

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. Si vous examinez attentivement, vous constatez qu’il y a plusieurs itinéraires en **bus** dans la reponse. Chaque itinéraire possède un **ID d'itinéraire** unique et s'accompagne d'un résumé détaillant chaque segment du trajet. Un segment correspond à la distance qui sépare deux arrêts. Nous allons ensuite demander des informations sur l'itinéraire le plus rapide en utilisant `itineraryId` dans la réponse.

## <a name="request-fastest-route-itinerary"></a>Demander l’itinéraire le plus rapide

Le service [Get Transit Itinerary](https://aka.ms/AzureMapsMobilityTransitItinerary) d’Azure Maps vous permet de demander des données relatives à un itinéraire spécifique via son **ID d’itinéraire**, obtenu via le service [API Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute). Pour effectuer une demande, procédez comme suit :

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Get Transit info**.

2. Dans l'onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**. Entrez l'URL de demande suivante pour le point de terminaison de l'API, puis cliquez sur **Envoyer**.

    Nous allons définir le paramètre `detailType` sur **geometry** afin que la réponse contienne des informations sur les arrêts de transports publics ainsi qu'une navigation étape par étape pour un itinéraire à pied et à vélo.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Après une demande réussie, la structure de la réponse doit ressembler à ce qui suit :

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment demander des données en temps réel à l’aide de Mobility Service :

> [!div class="nextstepaction"]
> [Comment demander des données en temps réel](how-to-request-real-time-data.md)

Explorer la documentation de l’API Mobility Service d’Azure Maps

> [!div class="nextstepaction"]
> [Documentation de l’API Mobility Service](https://aka.ms/AzureMapsMobilityService)

