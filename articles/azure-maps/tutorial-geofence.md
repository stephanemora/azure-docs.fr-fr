---
title: 'Tutoriel : Créer une limite géographique et suivre des appareils sur une carte | Microsoft Azure Maps'
description: Dans ce tutoriel, découvrez comment configurer une limite géographique et comment suivre des appareils en fonction de cette limite géographique à l’aide du service spatial Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0e408adfe1daed402ef690224368e846bd0a97c8
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910938"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutoriel : Configurer une limite géographique à l’aide d’Azure Maps

Ce tutoriel vous montre les étapes de base qui sont nécessaires à la configuration d’une limite géographique à l’aide d’Azure Maps. Dans ce tutoriel, le but est d’aider les chefs de chantier à s’assurer que le matériel potentiellement dangereux ne sorte pas des zones de construction prévues. Un site de construction implique du matériel coûteux et des réglementations. En général, le matériel doit rester sur le site de construction et ne pas le quitter sans autorisation.

Nous allons utiliser l’API Data Upload d’Azure Maps pour stocker une limite géographique, puis nous allons utiliser l’API Geofence d’Azure Maps pour vérifier l’emplacement du matériel par rapport à cette limite. Nous allons utiliser Azure Event Grid pour diffuser les résultats de la limite géographique et pour configurer une notification en fonction de ces résultats.
Pour plus d’informations sur Event Grid, consultez [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Charger la limite géographique dans le service de données Azure Maps à l’aide de l’API Data Upload
> *   Définir une grille d’événements pour gérer les événements relatifs à la limite géographique
> *   Configurer le gestionnaire des événements relatifs à la limite géographique
> *   Configurer des alertes en réponse aux événements de la limite géographique à l’aide de Logic Apps
> *   Utiliser les API du service de limite géographique d’Azure Maps pour vérifier si le matériel se trouve ou non sur le site de construction.


## <a name="prerequisites"></a>Conditions préalables requises

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps 

Pour effectuer les étapes de ce tutoriel, suivez les instructions mentionnées dans [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) pour créer un abonnement de compte Azure Maps avec le niveau tarifaire S1 et effectuez les étapes dans [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) afin d’obtenir la clé primaire pour votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Charger des limites géographiques

Pour charger la limite géographique du site de construction à l’aide de l’API Data Upload, nous allons utiliser l’application Postman. Dans ce tutoriel, nous partons du principe que le site de construction se trouve dans une zone dont le matériel n’est pas autorisé à sortir. Toute violation de cette limite constitue une infraction grave, qui doit être signalée au responsable des opérations. Un ensemble optimisé de limites supplémentaires peut être utilisé pour effectuer le suivi des différents sites situés dans la zone de construction, en fonction du planning. Nous supposons que la limite du site principal comprend la limite d’un sous-site (subsite1), dont la date d’expiration est définie. Vous pouvez imbriquer d’autres limites géographiques en fonction de vos besoins. Par exemple, subsite1 peut correspondre au site où doivent avoir lieu les travaux au cours des semaines 1 à 4 du planning, et subsite2 peut correspondre au site où doivent avoir lieu les travaux au cours des semaines 5 à 7. Toutes ces limites peuvent être chargées sous la forme d’un jeu de données au début du projet, et être utilisées pour effectuer le suivi des règles en fonction du lieu et de la date. Pour plus d’informations sur le format des données relatives aux limites géographiques, consultez [Données GeoJSON relatives aux limites géographiques](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Pour plus d’informations sur le chargement des données dans le service Azure Maps, consultez la [documentation relative à l’API Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview).

Ouvrez l’application Postman et suivez les étapes pour charger la limite géographique du site de construction à l’aide de l’API Data Upload d’Azure Maps.

1. Ouvrez l’application Postman, cliquez sur New (Nouveau) | Create New (Créer), puis sélectionnez Request (Requête). Entrez un nom de requête dans Upload geofence data (Charger les données de la limite géographique), sélectionnez la collection ou le dossier où enregistrer la requête, puis cliquez sur Save.

    ![Chargement de limites géographiques à l’aide de Postman](./media/tutorial-geofence/postman-new.png)

2. Sélectionnez la méthode HTTP POST sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Dans le chemin d’URL, le paramètre GEOJSON représente le format des données en cours de chargement.

3. Cliquez sur **Params**, puis entrez les paires clé/valeur suivantes à utiliser pour l’URL de la requête POST. Remplacez la valeur subscription-key par votre clé Azure Maps.
   
    ![Paramètres de chargement des données (limite géographique) dans Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Cliquez sur **Body** (Corps), sélectionnez le format d’entrée Raw (Brut), puis choisissez JSON comme format d’entrée dans la liste déroulante. Fournissez le code JSON suivant en tant que données à charger :

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Cliquez sur Send (Envoyer), puis examinez l’en-tête de la réponse. Quand une requête réussit, l’en-tête **Location** (Emplacement) contient l’URI d’état qui sert à vérifier l’état actuel de la requête de chargement. L’URI d’état sera au format suivant. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copiez votre URI d’état et ajoutez-lui un paramètre `subscription-key`. La valeur de ce paramètre doit correspondre à la clé d’abonnement de votre compte Azure Maps. Le format de l’URI d’état doit être semblable à celui ci-dessous :

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Pour obtenir la valeur `udId`, ouvrez un nouvel onglet dans l’application Postman, sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur), puis effectuez une requête GET sur l’URI d’état. Si le chargement de données réussit, vous recevez un udId dans le corps de la réponse. Copiez la valeur udId pour une utilisation ultérieure.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurer un gestionnaire d’événements

Pour informer le responsable des opérations des entrées et des sorties, nous devons créer un gestionnaire d’événements qui reçoit les notifications.

Nous allons créer deux services [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) pour gérer les événements d’entrée et de sortie. Nous allons également créer des déclencheurs d’événements dans les applications logiques qui sont déclenchées par ces événements. L’idée est d’envoyer des alertes, en l’occurrence des e-mails, au responsable des opérations, dès que du matériel rentre sur le site de construction ou en sort. L’exemple suivant montre la création d’une application logique pour les événements d’entrée dans la limite géographique. Vous pouvez en créer une autre pour les événements de sortie.
Pour plus d’informations, consultez tous les [gestionnaires d’événements pris en charge](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Créer une application logique dans le portail Azure

   ![Créer Azure Logic Apps pour gérer les événements liés aux limites géographiques](./media/tutorial-geofence/logic-app.png)

2. Sélectionnez un déclencheur de requête HTTP, puis sélectionnez « Envoyer un e-mail » dans le connecteur Outlook.
  
   ![Schéma des applications logiques](./media/tutorial-geofence/logic-app-schema.png)

3. Enregistrez l’application logique pour générer le point de terminaison de l’URL HTTP, puis copiez l’URL.

   ![Générer un point de terminaison Logic Apps](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Créer un abonnement aux événements Azure Maps

Azure Maps prend en charge trois types d’événements. Pour connaître les types d’événements Azure Maps pris en charge, [cliquez ici](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Nous allons créer deux abonnements différents, un pour les événements d’entrée et l’autre pour les événements de sortie.

Suivez les étapes ci-dessous pour créer un abonnement aux événements d’entrée dans la limite géographique. Vous pouvez vous abonner aux événements de sortie en suivant la même procédure.

1. Accédez à votre compte Azure Maps via [ce lien menant au portail](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/), puis sélectionnez l’onglet Événements.

   ![Accéder aux événements du compte Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Pour créer un abonnement aux événements, sélectionnez Abonnement aux événements dans la page des événements.

   ![Créer un abonnement aux événements Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Attribuez un nom à l’abonnement aux événements, puis abonnez-vous au type d’événements d’entrée. À présent, sélectionnez Webhook comme « Type de point de terminaison », puis copiez le point de terminaison de l’URL HTTP de l’application logique dans « Point de terminaison »

   ![Détails de l’abonnement aux événements Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Utiliser l’API Geofence

Vous pouvez utiliser l’API Geofence pour vérifier si un **appareil** (le matériel de construction est considéré comme tel) se trouve à l’intérieur ou en dehors de la limite géographique. Pour mieux comprendre l’API GET Geofence, nous lui envoyons une requête pour connaître les différents emplacements vers lesquels l’équipement a été déplacé. L’illustration suivante montre les cinq emplacements d’un engin de construction, ayant chacun un **ID d’appareil** unique, classés dans l’ordre chronologique. Chacun de ces cinq emplacements est utilisé pour évaluer les états d’entrée et de sortie par rapport à la limite géographique. Si un changement d’état se produit, le service Geofence déclenche un événement, qui est envoyé à l’application logique par la grille d’événements. En conséquence, le responsable des opérations recevra une notification d’entrée ou de sortie par e-mail.

> [!Note]
> Ce scénario et ce comportement sont basés sur le même **ID d’appareil** afin de montrer les cinq emplacements différents, comme dans la figure ci-dessous.

![Mappage de limite géographique dans Azure Maps](./media/tutorial-geofence/geofence.png)

Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créée précédemment. Sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur) :

Voici cinq requêtes HTTP GET de l’API Geofence, avec différentes coordonnées correspondant aux emplacements de l’engin, classées par ordre chronologique. Chaque requête est suivie du corps de la réponse.
 
1. Emplacement 1 :
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence - Requête 1](./media/tutorial-geofence/geofence-query1.png)

   Dans la réponse ci-dessus, la distance négative par rapport à la limite géographique principale signifie que le matériel se trouve dans cette limite géographique, et la valeur positive par rapport à la limite géographique du sous-site signifie que le matériel se trouve en dehors de la limite du sous-site. 

2. Emplacement 2 : 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence - Requête 2](./media/tutorial-geofence/geofence-query2.png)

   Si vous examinez attentivement la réponse JSON précédente, le matériel se trouve en dehors du sous-site, mais à l’intérieur de la limite principale. Dans cas, aucun événement n’est déclenché et aucun e-mail n’est envoyé.

3. Emplacement 3 : 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence - Requête 3](./media/tutorial-geofence/geofence-query3.png)

   L’état du matériel a changé, car celui-ci se trouve à la fois en dehors du sous-site et en dehors du site principal. Dans ce cas, un événement et un e-mail de notification sont envoyés au responsable des opérations.

4. Emplacement 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence - Requête 4](./media/tutorial-geofence/geofence-query4.png)

   Si on observe attentivement la réponse correspondante, on voit qu’aucun événement n’est publié, même si le matériel se trouve désormais en dehors de la limite géographique du matériel. Si vous regardez l’heure spécifiée par l’utilisateur dans la requête GET, vous pouvez voir que la limite du sous-site a expiré et que le matériel se trouve toujours dans la limite du site principal. Vous pouvez également voir l’ID de géométrie de la limite géographique du sous-site sous `expiredGeofenceGeometryId`, dans le corps de réponse.


5. Emplacement 5 :
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence - Requête 5](./media/tutorial-geofence/geofence-query5.png)

   Vous pouvez voir que le matériel a quitté le site de construction principal. Dans ce cas, il s’agit d’une violation grave, qui provoque la publication d’un événement et l’envoi d’un e-mail au responsable des opérations.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer une limite géographique en la chargeant dans le service Data d’Azure Maps à l’aide de l’API Data Upload. Vous avez également vu comment utiliser les grilles d’événements Azure Maps auxquelles vous abonner et comment gérer les événements liés aux limites géographiques. 

* Pour savoir comment utiliser Logic Apps pour analyser JSON en vue de créer une logique plus complexe, consultez [Gérer les types de contenu dans Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type).
* Pour plus d’informations sur les gestionnaires d’événements dans Event Grid, consultez [Gestionnaires d’événements pris en charge dans Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
