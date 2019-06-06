---
title: Comment demander des données en temps réel dans Azure Maps | Microsoft Docs
description: Demander des données en temps réel à l’aide du service de mobilité de Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4d8d34d8dec52dd9173cea80c39097f46d32bff5
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735480"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Demander des données en temps réel à l’aide du Service de mobilité Azure Maps

Cet article vous montre comment utiliser Azure Maps [Service mobilité](https://aka.ms/AzureMapsMobilityService) pour demander des données de transit en temps réel.

Dans cet article, vous apprendrez comment :


 * Demande suivantes arrivées en temps réel pour toutes les lignes qui arrivent en fin donné
 * Demander des informations en temps réel pour une station d’accueil de vélo donné.


## <a name="prerequisites"></a>Conditions préalables

Pour rendre tous les appels à l’API de transit public Azure Maps, vous avez besoin d’un compte Maps et une clé. Pour plus d’informations sur la création d’un compte et la récupération d’une clé, consultez [How to manage your Azure Maps account and keys](how-to-manage-account-keys.md) (Gérer votre compte et vos clés Azure Maps).

Cet article utilise [l’application Postman](https://www.getpostman.com/apps) pour générer des appels REST. Vous pouvez utiliser l’environnement de développement d’API que vous préférez.


## <a name="request-real-time-arrivals-for-a-stop"></a>Arrivées de requêtes en temps réel pour un arrêt

Pour demander des données arrivées en temps réel pour un arrêt de transit public particulier, vous devrez effectuer une requête pour le [en temps réel arrivées API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) des mappages Azure [Service mobilité](https://aka.ms/AzureMapsMobilityService). Vous devez le **metroID** et **stopID** pour terminer la demande. Pour en savoir plus sur comment demander ces paramètres, consultez notre savoir-faire guide [demander des routes de transit publique](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Nous allons utiliser « 522 » en tant que notre metro ID, qui est le métro ID de zone de « Seattle – Tacoma – Bellevue, WA », puis utilisez l’ID d’arrêt « 2060603 », qui est un bus arrêter à « nou 24 St & 162nd Ave nou, Bellevue WA ». Pour demander des données en temps réel arrivées ensuite cinq pour toutes les arrivées dynamiques suivantes cette étape, procédez comme suit :

1. Créer un regroupement dans lequel stocker les demandes. Dans l’application Postman, sélectionnez **New**. Dans le **créer un nouveau** fenêtre, sélectionnez **Collection**. Nom de la collection et sélectionnez le **créer** bouton.

2. Pour créer la demande, sélectionnez **New** à nouveau. Dans le **créer un nouveau** fenêtre, sélectionnez **demande**. Entrez un **nom de la demande** pour la demande, sélectionnez le regroupement que vous avez créé à l’étape précédente en tant que l’emplacement dans lequel enregistrer la demande, puis sélectionnez **enregistrer**.

    ![Créer une requête dans Postman](./media/how-to-request-transit-data/postman-new.png)

3. Sélectionnez la méthode HTTP GET sur l’onglet Générateur de rapports et entrez l’URL suivante pour créer une demande GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Après une demande réussie, vous recevrez la réponse suivante.  Notez que ce paramètre scheduleType définit si l’heure d’arrivée estimé est basé sur des données en temps réel ou statiques.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }


## Real-time availability and vacancy information for bike docking station

The [Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) of the Azure Maps Mobility Service, allows to request static and real-time information for a given bike or scooter docking station. We will make a request to get real-time data for a docking station for bikes. 

In order to make a request to the Get Transit Dock Info API, you will need the **dockId** for that station. You can get the dock ID by making a search request to the [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) and setting the **objectType** parameter to "bikeDock". Follow the steps below to get real-time data of a docking station for bikes.


### Get dock ID

To get **dockID**, follow the steps below to make a request to the Get Nearby Transit API:

1. In Postman, click **New Request** | **GET request** and name it **Get dock ID**.

2.  On the Builder tab, select the **GET** HTTP method, enter the following request URL, and click **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Après une demande réussie, vous recevrez la réponse suivante. Notez que nous avons maintenant le **id** dans la réponse, ce qui peut servir ultérieurement comme paramètre de requête dans la demande à l’API Get Dock de Transit Info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
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


### <a name="get-real-time-bike-dock-status"></a>Obtenir l’état d’ancrage de vélo en temps réel

Suivez les étapes ci-dessous pour effectuer une demande à l’API obtention du Transit ancrer Info pour obtenir des données en temps réel pour l’écran d’ancrage sélectionné.

1. Dans Postman, cliquez sur **nouvelle demande** | **demande GET** et nommez-le **obtenir des données d’ancrage en temps réel**.

2.  Sous l’onglet Générateur de rapports, sélectionnez le **obtenir** méthode HTTP, entrez l’URL de requête suivante, puis cliquez sur **envoyer**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Après une demande réussie, vous recevrez une réponse de la structure suivante :

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment demander des données de transit à l’aide du Service mobilité :

> [!div class="nextstepaction"]
> [Comment demander des données de transit](how-to-request-transit-data.md)

Explorez la documentation de l’API du Service mobilité Azure Maps :

> [!div class="nextstepaction"]
> [Documentation de l’API du Service mobilité](https://aka.ms/AzureMapsMobilityService)
