---
title: 'Tutoriel : Créer une limite géographique et suivre des appareils sur une carte | Microsoft Azure Maps'
description: Découvrez comment définir une limite géographique et suivre des appareils en fonction de cette limite géographique à l’aide du service spatial Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a88f03adab3beaea75ec2fa9a1c6f59b09739025
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153132"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutoriel : Configurer une limite géographique à l’aide d’Azure Maps

Ce tutoriel vous montre les étapes de base qui sont nécessaires à la configuration d’une limite géographique à l’aide d’Azure Maps. Imaginez le scénario suivant : un responsable de site de construction doit superviser du matériel potentiellement dangereux. Il doit s’assurer que le matériel reste dans la zone de construction globale choisie. Cette zone de construction globale est un paramètre incontournable. Les réglementations exigent que l’équipement respecte ce paramètre, et toute violation est signalée au responsable des opérations.  

Nous utilisons l’API Data Upload pour stocker une limite géographique, puis l’API Geofence pour vérifier l’emplacement du matériel par rapport à cette limite. Les API Data Upload et Geofence proviennent d’Azure Maps. Nous utilisons aussi Azure Event Grid pour diffuser en streaming les résultats de la limite géographique et pour configurer une notification en fonction de ces résultats. Pour plus d’informations sur Event Grid, consultez [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Charger la limite géographique dans le service de données Azure Maps à l’aide de l’API Data Upload
> *   Définir une grille d’événements pour gérer les événements relatifs à la limite géographique
> *   Configurer le gestionnaire des événements relatifs à la limite géographique
> *   Configurer des alertes en réponse aux événements de la limite géographique à l’aide de Logic Apps
> *   Utiliser les API du service de limite géographique d’Azure Maps pour vérifier si le matériel se trouve ou non sur le site de construction.


## <a name="prerequisites"></a>Conditions préalables requises

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps 

Suivez les instructions fournies dans [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) pour créer un abonnement de compte Azure Maps au niveau tarifaire S1. Les étapes décrites dans [Obtenir une clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) montrent comment récupérer la clé primaire de votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Charger des limites géographiques

Nous partons du principe que la limite géographique principale est subsite1, qui a un délai d’expiration défini. Vous pouvez imbriquer d’autres limites géographiques en fonction de vos besoins. Vous pouvez utiliser ces ensembles de limites pour effectuer le suivi de différents sites situés dans la zone de construction globale. Par exemple, subsite1 peut correspondre au site où doivent avoir lieu les travaux au cours des semaines 1 à 4 du planning, , tandis que subsite2 peut correspondre au site où doivent avoir lieu les travaux au cours des semaines 5 à 7. Toutes ces limites peuvent être chargées sous la forme d’un jeu de données au début du projet. Vous pouvez utiliser ces limites pour effectuer le suivi des règles en fonction du lieu et de la date. 

Pour charger la limite géographique du site de construction à l’aide de l’API Data Upload, nous utilisons l’application Postman. Installez l’[application Postman](https://www.getpostman.com/) et créez un compte gratuit. 

Une fois l’application Postman installée, suivez ces étapes pour charger la limite géographique du site de construction à l’aide de l’API Data Upload d’Azure Maps.

1. Ouvrez l’application Postman, cliquez sur New (Nouveau) | Create New (Créer), puis sélectionnez Request (Requête). Entrez un nom de requête dans Upload geofence data (Charger les données de la limite géographique), sélectionnez la collection ou le dossier où enregistrer la requête, puis cliquez sur Save.

    ![Chargement de limites géographiques à l’aide de Postman](./media/tutorial-geofence/postman-new.png)

2. Sélectionnez la méthode HTTP POST sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Dans le chemin d’URL, le paramètre GEOJSON représente le format des données en cours de chargement.

3. Cliquez sur **Params**, puis entrez les paires clé/valeur suivantes à utiliser pour l’URL de la requête POST. Remplacez {subscription-Key} par votre clé d’abonnement Azure Maps, également appelée clé primaire.
   
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

5. Cliquez sur Send (Envoyer), puis examinez l’en-tête de la réponse. Une fois que la demande a réussi, l’en-tête **Location** contient l’URI d’état. L’URI d’état a le format suivant. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copiez votre URI d’état et ajoutez la clé d’abonnement. Le format de l’URI d’état doit être semblable à celui ci-dessous. Notez que dans le format ci-dessous, vous devez remplacer {subscription-Key}, y compris { }, par votre clé d’abonnement.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Pour obtenir la valeur `udId`, ouvrez un nouvel onglet dans l’application Postman et sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur). Effectuez une requête GET sur l’URI d’état obtenu à l’étape précédente. Si le chargement des données réussit, vous recevez l’udId dans le corps de la réponse. Copiez la valeur udId pour une utilisation ultérieure.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurer un gestionnaire d’événements

Dans cette section, nous allons créer un gestionnaire d’événements qui reçoit des notifications. Ce gestionnaire d’événements doit informer le responsable des opérations de tout événement d’entrée et de sortie du matériel.

Nous créons deux services [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) pour gérer les événements d’entrée et de sortie. Quand les événements Logic Apps se déclenchent, d’autres événements se déclenchent en séquence. L’idée est d’envoyer des alertes (en l’occurrence des e-mails) aux responsable des opérations. L’exemple suivant montre la création d’une application logique pour les événements d’entrée dans la limite géographique. Vous pouvez en créer une autre pour les événements de sortie. Pour plus d’informations, consultez tous les [gestionnaires d’événements pris en charge](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Créer une application logique dans le portail Azure

   ![Créer Azure Logic Apps pour gérer les événements liés aux limites géographiques](./media/tutorial-geofence/logic-app.png)

2. Dans le menu de paramètres de l’application logique, accédez à **Concepteur d’application logique**.

3. Sélectionnez un déclencheur de requête HTTP, puis sélectionnez « Nouvelle étape ». Dans le connecteur Outlook, sélectionnez « Envoyer un e-mail » comme action.
  
   ![Schéma des applications logiques](./media/tutorial-geofence/logic-app-schema.png)

4. Renseignez les champs pour l’envoi d’un e-mail. Laissez l’URL HTTP. Elle sera générée automatiquement une fois que vous aurez cliqué sur « Enregistrer ».

   ![Générer un point de terminaison Logic Apps](./media/tutorial-geofence/logic-app-endpoint.png)

5. Enregistrez l’application logique pour générer le point de terminaison de l’URL HTTP, puis copiez l’URL.

## <a name="create-an-azure-maps-events-subscription"></a>Créer un abonnement aux événements Azure Maps

Azure Maps prend en charge trois types d’événements. Pour connaître les types d’événements Azure Maps pris en charge, consultez cette page : https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps. Nous avons besoin de deux abonnements d’événements différents, un pour l’événement d’entrée et un pour les événements de sortie.

Suivez les étapes ci-dessous pour créer un abonnement aux événements d’entrée dans la limite géographique. Vous pouvez vous abonner aux événements de sortie en suivant la même procédure.

1. Accédez à votre compte Azure Maps. Dans le tableau de bord, sélectionnez Abonnements. Cliquez sur le nom de votre abonnement, puis sélectionnez **Événements** dans le menu de paramètres.

   ![Accéder aux événements du compte Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Pour créer un abonnement aux événements, sélectionnez Abonnement aux événements dans la page des événements.

   ![Créer un abonnement aux événements Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Attribuez un nom à l’abonnement aux événements, puis abonnez-vous au type d’événements d’entrée. À présent, sélectionnez Web Hook comme « Type de point de terminaison ». Cliquez sur « Sélectionner un point de terminaison » et copiez le point de terminaison de l’URL HTTP de votre application logique dans « {Point de terminaison} ».

   ![Détails de l’abonnement aux événements Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Utiliser l’API Geofence

Vous pouvez utiliser l’API Geofence pour vérifier si un **appareil** (en l’occurrence le matériel de construction) se trouve à l’intérieur ou en dehors de la limite géographique. Interrogeons l’API GET Geofence par rapport à plusieurs endroits dans lesquels l’équipement a été déplacé au fil du temps. La figure suivante illustre cinq endroits avec cinq engins de construction. 

> [!Note]
> Ce scénario et ce comportement sont basés sur le même **ID d’appareil** afin de montrer les cinq endroits différents, comme dans la figure ci-dessous.

« deviceId » est un ID unique que vous spécifiez pour votre appareil dans la requête GET lors de l’interrogation de sa localisation. Quand vous effectuez une requête asynchrone auprès de l’**API GET - Rechercher dans une limite géographique**, le « deviceId » vous aide à publier des événements de limite géographique pour cet appareil, relativement à la limite géographique spécifiée. Dans ce tutoriel, nous avons effectué des requêtes asynchrones à l’API avec un « deviceId » unique. Les requêtes du tutoriel sont effectuées dans l’ordre chronologique, comme indiqué dans le schéma. La propriété « isEventPublished » de la réponse est publiée chaque fois qu’un appareil entre ou quitte la limite géographique. Vous n’avez pas besoin d’inscrire un appareil pour suivre ce tutoriel.

Revenons au schéma. Chacun de ces cinq emplacements est utilisé pour évaluer les états d’entrée et de sortie par rapport à la limite géographique. Si un changement d’état se produit, le service Geofence déclenche un événement, qui est envoyé à l’application logique par la grille d’événements. En conséquence, le responsable des opérations recevra une notification d’entrée ou de sortie par e-mail.

![Mappage de limite géographique dans Azure Maps](./media/tutorial-geofence/geofence.png)

Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créée précédemment. Sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur) :

Voici cinq requêtes HTTP GET de l’API Geofence, avec différentes coordonnées de localisation du matériel. Les coordonnées sont telles qu’observées dans l’ordre chronologique. Chaque requête est suivie du corps de la réponse.
 
1. Emplacement 1 :
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence - Requête 1](./media/tutorial-geofence/geofence-query1.png)

   Dans la réponse ci-dessus, la distance négative par rapport à la limite géographique signifie que le matériel se trouve à l’intérieur de la limite géographique. La distance positive par rapport à la limite géographique de sous-site signifie que le matériel se trouve en dehors de la limite géographique de sous-site. 

2. Emplacement 2 : 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence - Requête 2](./media/tutorial-geofence/geofence-query2.png)

   Si vous examinez attentivement la réponse JSON précédente, vous constatez que l’engin se trouve en dehors du sous-site, mais à l’intérieur de la limite principale. Aucun événement n’est déclenché et aucun e-mail n’est envoyé.

3. Emplacement 3 : 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence - Requête 3](./media/tutorial-geofence/geofence-query3.png)

   L’état du matériel a changé, car celui-ci se trouve à la fois en dehors du sous-site et en dehors du site principal. Ce changement entraîne la publication d’un événement, et un e-mail de notification sera envoyé au responsable des opérations.

4. Emplacement 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence - Requête 4](./media/tutorial-geofence/geofence-query4.png)

   Si on observe attentivement la réponse correspondante, on voit qu’aucun événement n’est publié, même si le matériel se trouve désormais en dehors de la limite géographique du matériel. Si vous regardez l’heure spécifiée par l’utilisateur dans la requête GET, vous constatez que la limite du sous-site a expiré pour cette heure. Le matériel est toujours dans la limite géographique principale. Vous pouvez également voir l’ID de géométrie de la limite géographique du sous-site sous `expiredGeofenceGeometryId`, dans le corps de réponse.


5. Emplacement 5 :
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence - Requête 5](./media/tutorial-geofence/geofence-query5.png)

   Vous pouvez voir que le matériel a quitté le site de construction principal. Un événement est publié et un e-mail d’alerte est envoyé au responsable des opérations.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à définir une limite géographique en la chargeant dans les services Azure Maps et Data à l’aide de l’API Data Upload. Vous avez également vu comment utiliser les grilles d’événements Azure Maps auxquelles vous abonner et comment gérer les événements liés aux limites géographiques. 

* Pour savoir comment utiliser Logic Apps pour analyser JSON en vue de créer une logique plus complexe, consultez [Gérer les types de contenu dans Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type).
* Pour plus d’informations sur les gestionnaires d’événements dans Event Grid, consultez [Gestionnaires d’événements pris en charge dans Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
