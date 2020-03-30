---
title: Demander des données de transit en temps réel | Microsoft Azure Maps
description: Demandez des données en temps réel avec le service Mobility de Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335481"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Demander des données en temps réel avec le service Mobility d’Azure Maps

Cet article vous montre comment utiliser le [service Mobility](https://aka.ms/AzureMapsMobilityService) d’Azure Maps pour demander des données en temps réel sur les transports.

Cet article porte sur les points suivants :


 * Demander les horaires d’arrivée en temps réel de toutes les lignes de bus à un arrêt donné
 * Demander des informations en temps réel sur une station de vélos donnée


## <a name="prerequisites"></a>Conditions préalables requises

Vous devez d’abord disposer d’un compte Azure Maps et d’une clé d’abonnement pour effectuer des appels vers les API de transports publics Azure Maps. Pour plus d'informations sur la création d'un compte Azure Maps, suivez les instructions de la section [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps). Suivez les étapes de la section [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) afin de vous procurer la clé primaire de votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).


Cet article utilise [l’application Postman](https://www.getpostman.com/apps) pour générer des appels REST. Vous pouvez utiliser l’environnement de développement d’API que vous préférez.


## <a name="request-real-time-arrivals-for-a-stop"></a>Demander les horaires d’arrivée en temps réel à un arrêt donné

Pour demander les horaires d’arrivée en temps réel à un arrêt de transport public particulier, vous devez envoyer une demande à l’[API Real-time Arrivals](https://aka.ms/AzureMapsMobilityRealTimeArrivals) du [service Mobility](https://aka.ms/AzureMapsMobilityService) d’Azure Maps. Vous devez indiquer les paramètres **metroID** et **stopID** dans la demande. Pour en savoir plus sur l’obtention de ces paramètres, consultez notre guide sur la façon de [demander des itinéraires des transports publics](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Utilisons « 522 » comme numéro de zone urbaine, qui est le numéro de la zone « Seattle–Tacoma–Bellevue, WA ». Utilisons « 522---2060603 » comme numéro d’arrêt ; cet arrêt de bus se trouve à « Ne 24th St & 162nd Ave Ne, Bellevue WA ». Pour demander les horaires d’arrivée en temps réel des cinq prochains passages de bus à cet arrêt, effectuez les étapes suivantes :

1. Ouvrez l'application Postman, puis créez une collection destinée au stockage des demandes. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Collection**.  Nommez la collection puis sélectionnez le bouton **Create** (Créer).

2. Pour créer la demande, sélectionnez **New** à nouveau. Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande). Entrez un **Request name** (Nom de demande) pour la demande. Sélectionnez la collection que vous avez créée à l’étape précédente comme emplacement dans lequel enregistrer la demande. Sélectionnez ensuite **Enregistrer**.

    ![Créer une demande dans Postman](./media/how-to-request-transit-data/postman-new.png)

3. Sélectionnez la méthode HTTP **GET** sous l'onglet Builder (Générateur), puis entrez l'URL suivante pour créer une requête GET. Remplacez `{subscription-key}` par votre clé primaire Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Après une demande réussie, vous recevrez la réponse suivante.  Notez que le paramètre scheduleType définit si l’horaire d’arrivée est estimé d’après des données en temps réel ou statiques.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Données en temps réel relatives à une station de vélos

L’API [Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) permet aux utilisateurs de demander des informations statiques et en temps réel. Par exemple, les utilisateurs peuvent demander des informations sur la disponibilité et les emplacements libres à une station de vélos ou de scooters. L’[API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) fait également partie du [service Mobility](https://aka.ms/AzureMapsMobilityService) d’Azure Maps.

Pour envoyer une requête à l’[API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock), vous avez besoin du paramètre **dockId**, qui identifie la station. Vous pouvez obtenir le numéro de la station en envoyant une demande de recherche à l’[API Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) avec le paramètre **objectType** défini sur « bikeDock ». Effectuez les étapes ci-dessous pour obtenir des données en temps réel sur une station de vélos.


### <a name="get-dock-id"></a>Obtenir l’ID de la station

Pour obtenir le paramètre **dockID**, envoyez une requête à l’API Get Nearby Transit en effectuant les étapes ci-dessous :

1. Dans Postman, cliquez sur **New Request** | **GET request** (Nouvelle requête > Requête GET) et nommez-la **Get dock ID** (Obtenir l’ID de la station).

2.  Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête puis cliquez sur **Send** (Envoyer).
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Après une demande réussie, vous recevrez la réponse suivante. Notez que l’**id** obtenu dans la réponse pourra être utilisé plus tard comme paramètre dans la requête envoyée à l’API Get Transit Dock Info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Obtenir l’état en temps réel d’une station d’accueil de vélo

À l’aide des étapes ci-dessous, envoyez une requête à l’API Get Transit Dock Info pour obtenir des données en temps réel sur la station d’accueil sélectionnée.

1. Dans Postman, cliquez sur **New Request** | **GET request** (Nouvelle requête > Requête GET) et nommez-la **Get real-time dock data** (Obtenir des données en temps réel sur une station d’accueil).

2.  Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête puis cliquez sur **Send** (Envoyer).
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Après une demande réussie, vous recevrez une réponse similaire à celle-ci :

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment demander des données de transit à l’aide de Mobility Service :

> [!div class="nextstepaction"]
> [Comment demander des données de transit](how-to-request-transit-data.md)

Explorer la documentation de l’API Mobility Service d’Azure Maps :

> [!div class="nextstepaction"]
> [Documentation de l’API Mobility Service](https://aka.ms/AzureMapsMobilityService)
