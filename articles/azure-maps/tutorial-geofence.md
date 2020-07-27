---
title: 'Tutoriel : Créer une limite géographique et suivre des appareils sur une carte Microsoft Azure'
description: Découvrez comment définir une limite géographique et suivre des appareils en fonction de cette limite géographique à l’aide du service spatial Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 775d98b992f2bca4441c868873ceaeb2389db81a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517355"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutoriel : Configurer une limite géographique à l’aide d’Azure Maps

Ce tutoriel vous présente les principes de base de la création et de l’utilisation des services de limite géographique Azure Maps dans le cadre du scénario suivant :

*Un responsable de site de construction doit assurer le suivi de ses équipements pour savoir quand ils entrent dans la zone de construction et quand ils en sortent. Chaque fois qu’un équipement entre dans ces périmètres ou en sort, une notification par e-mail sera envoyée au chef d’exploitation.*

Azure Maps offre un certain nombre de services permettant de suivre les équipements qui entrent dans la zone de construction et en sortent, répondant ainsi aux besoins du scénario ci-dessus. Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Charger les [données de geofencing GeoJSON](geofence-geojson.md) qui définissent les zones du site de construction que nous souhaitons superviser. Vous utiliserez l’[API Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) pour charger les limites géographiques sous forme de coordonnées de polygone sur votre compte Azure Maps.
> * Configurer deux [applications logiques](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) qui, quand elles sont déclenchées (quand un équipement entre dans la limite géographique ou en sort), envoient des notifications par e-mail au chef d’exploitation du site de construction.
> * Utiliser la [grille d’événement Azure](https://docs.microsoft.com/azure/event-grid/overview) pour l’abonnement aux événements d’entrée et de sortie de limite géographique Azure Maps. Nous configurerons deux abonnements aux événements Webhook, qui appelleront les points de terminaison HTTP définis dans vos deux applications logiques. Les applications logiques enverront alors les notifications par e-mail appropriées, relatives à l’équipement qui entre dans le limite géographique ou en sort.
> * Utiliser l’[API Get Search Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) pour recevoir des notifications quand un équipement entre dans les zones de limite géographique ou en sort.

## <a name="prerequisites"></a>Prérequis

1. [Créer un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenir une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement.

Ce tutoriel utilise l’application [Postman](https://www.postman.com/), mais vous pouvez choisir un autre environnement de développement d’API.

## <a name="upload-geofencing-geojson-data"></a>Charger les données de geofencing GeoJSON

Dans ce tutoriel, nous allons charger les données de geofencing GeoJSON contenant une `FeatureCollection`. La `FeatureCollection` contient deux limites géographiques qui définissent des zones polygonales dans le site de construction. Aucune expiration ni restriction ne sont associées à la première limite géographique. La deuxième ne peut être interrogée que pendant les heures d’ouverture (de 9 h à 17 h PST) et ne sera plus valide à compter du 1er janvier 2022. Pour plus d’informations sur le format GeoJSON, consultez [Données Geofencing GeoJSON](geofence-geojson.md).

>[!TIP]
>Vous pouvez mettre à jour vos données de geofencing à tout moment. Pour plus d’informations sur la mise à jour de vos données, consultez [API Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview).

1. Ouvrez l’application Postman. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Collection**.  Nommez la collection puis sélectionnez le bouton **Create** (Créer).

2. Pour créer la demande, sélectionnez **New** à nouveau. Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande). Entrez un **Request name** (Nom de demande) pour la demande. Sélectionnez la collection que vous avez créée à l’étape précédente, puis sélectionnez **Enregistrer**.

3. Sélectionnez la méthode HTTP **POST** sous l’onglet du générateur, puis entrez l’URL suivante pour charger les données de geofencing vers le service Azure Maps. Pour cette requête et d’autres requêtes mentionnées dans cet article, remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Le paramètre _geojson_ de l’URL représente le format des données en cours de chargement.

4. Cliquez sur l’onglet **Corps**. Sélectionnez **brut**, puis **JSON** comme format d’entrée. Copiez les données GeoJSON suivantes et collez-les dans la zone de texte **Corps** :

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
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
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

5. Cliquez sur le bouton bleu **Envoyer**, puis attendez que la requête soit traitée. Une fois la requête terminée, accédez à l’onglet **En-têtes** de la réponse. Copiez la valeur de la **Emplacement**, qui est l’URL `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Pour vérifier l’état de l’appel d’API, créez une requête HTTP **GET** sur `status URL`. Vous devez ajouter votre clé d’abonnement principale à l’URL pour l’authentification. La requête **GET** doit ressembler à l’URL suivante :

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Lorsque la requête HTTP **GET** se termine avec succès, un `resourceLocation`est retourné. Le `resourceLocation` contient également la valeur unique `udid` pour les données chargées. Vous devez enregistrer cet `udid` pour pouvoir interroger l’API Get Geofence dans la dernière section de ce tutoriel. Vous pouvez éventuellement utiliser l’URL `resourceLocation` pour récupérer les métadonnées de cette ressource à l’étape suivante.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Pour récupérer les métadonnées de contenu, créez une requête HTTP **GET** sur l’URL `resourceLocation` que vous avez récupérée à l’étape 7. Vous devez ajouter votre clé d’abonnement principale à l’URL pour l’authentification. La requête **GET** doit ressembler à l’URL suivante :

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Lorsque la requête HTTP **GET** se termine avec succès, le corps de la réponse contient un `udid` spécifié au `resourceLocation` de l’étape 7, l’emplacement où accéder au contenu et le télécharger à l’avenir, ainsi que d’autres métadonnées relatives au contenu, telles que la date de création/mise à jour, la taille, etc. Voici un exemple de réponse globale :

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>Créer des workflows d’application logique

Dans cette section, nous allons créer deux points de terminaison d’[application logique](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) qui déclencheront une notification par e-mail. Nous verrons comment créer le premier déclencheur qui enverra des notifications par e-mail chaque fois que son point de terminaison sera appelé.

1. Connectez-vous au [portail Azure](https://portal.azure.com)

2. En haut à gauche du [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**.

3. Dans la zone *Recherche dans la Place de marché*, tapez **Application logique**.

4. Sous *Résultats*, sélectionnez **Application logique**. Cliquez sur le bouton **Créer**.

5. Dans la page **Application logique**, entrez les valeurs suivantes :
    * Sous *Abonnement*, l’abonnement à utiliser pour cette application logique.
    * Sous *Groupe de ressources*, le nom du groupe de ressources pour cette application logique. Vous pouvez choisir de *Créer* ou d’utiliser un groupe de ressources *Existant*.
    * Sous *Nom de l’application logique*, le nom de votre application logique. Dans le cas présent, nous utiliserons le nom `Equipment-Enter`.

    Pour les besoins de ce tutoriel, conservez les autres valeurs par défaut.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Créer une application logique":::

6. Cliquez sur le bouton **Vérifier + créer**. Vérifiez vos paramètres, puis cliquez sur **Créer** pour envoyer le déploiement. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**. Vous accédez alors au **concepteur d’application logique**.

7. Nous allons maintenant sélectionner un type de déclencheur. Faites légèrement défiler la page vers le bas pour faire apparaître la section *Démarrer avec un déclencheur courant**. Cliquez sur **Lors de la réception d’une demande HTTP**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Créer un déclencheur HTTP pour une application logique":::

8. Cliquez sur **Enregistrer** en haut à droite du concepteur. L’**URL HTTP POST** est générée automatiquement. Enregistrez l’URL. Vous en aurez besoin dans la section suivante pour créer un point de terminaison d’événement.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Application logique - URL et JSON de la requête HTTP":::

9. Sélectionnez **+ Nouvelle étape**. Nous allons à présent choisir une action. Tapez `outlook.com email` dans la zone de recherche. Faites défiler la liste **Actions** vers le bas, puis cliquez sur **Envoyer un e-mail (v2)** .
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Création d’une application logique - concepteur":::

10. Connectez-vous à votre compte Outlook.com. Veillez à cliquer sur **Oui** pour autoriser l’application logique à accéder au compte. Renseignez les champs pour l’envoi d’un e-mail.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Création d’une application logique - étape d’envoi d’un e-mail":::

    >[!TIP]
    > Vous pouvez récupérer des données de réponse GeoJSON comme `geometryId` ou `deviceId` dans vos notifications par e-mail en configurant l’application logique pour lire les données envoyées par la grille d’événement. Pour plus d’informations sur la configuration de l’application logique pour consommer et transmettre des données d’événement dans des notifications par e-mail, consultez [Tutoriel : Envoyer des notifications par e-mail concernant des événements Azure IoT Hub à l’aide d’Event Grid et de Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. Cliquez sur **Enregistrer** en haut à gauche du concepteur d’application logique.

12. Répétez les étapes 3-11 pour créer une deuxième application logique afin de notifier le chef d’exploitation quand un équipement sort du site de construction. Nommez l’application logique `Equipment-Exit`.

## <a name="create-azure-maps-events-subscriptions"></a>Créer des abonnements aux événements Azure Maps

Azure Maps prend en charge trois types d’événements. Pour connaître les types d’événements Azure Maps pris en charge, [cliquez ici](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps).  Nous devons créer deux abonnements aux événements : un pour les événements d’entrée dans la limite géographique et un pour les événements de sortie.

Suivez les étapes ci-dessous pour créer un abonnement aux événements d’entrée dans la limite géographique. Vous pouvez vous abonner aux événements de sortie en suivant la même procédure.

1. Accédez à votre compte Azure Maps. Dans le tableau de bord, sélectionnez **Abonnements**. Cliquez sur le nom de votre abonnement, puis sélectionnez **Événements** dans le menu de paramètres.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Accès aux événements du compte Azure Maps":::

2. Pour créer un abonnement aux événements, sélectionnez **+ Abonnement aux événements** dans la page des événements.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Créer un abonnement aux événements Azure Maps":::

3. Dans la page **Créer un abonnement aux événements**, entrez les valeurs suivantes :
    * *Nom* de l’abonnement.
    * *Schéma d’événement* : *Schéma Event Grid*.
    * *Nom de la rubrique système* pour cet abonnement aux événements. Dans ce cas, nous utilisons `Contoso-Construction`.
    * Sous *Filtrer sur les types d’événements*, choisissez le type d’événement `Geofence Entered`.
    * Sous *Type de point de terminaison*, choisissez `Web Hook`.
    * Pour *Point de terminaison*, copiez l’URL HTTP POST du point de terminaison créé à la section précédente pour l’application logique Enter. Si vous avez oublié de l’enregistrer, vous pouvez simplement revenir au concepteur d’application logique et le copier (étape relative au déclencheur HTTP).

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Détails de l’abonnement aux événements Azure Maps":::

4. Cliquez sur **Créer**.

5. Répétez les étapes 1 à 4 pour le point de terminaison créé à la section précédente pour l’application logique Exit. À l’étape 3, veillez à choisir le type d’événement `Geofence Exited`.

## <a name="use-search-geofence-get-api"></a>Utiliser l’API Get Search Geofence

À présent, nous allons utiliser l’[API Get Search Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) pour envoyer des notifications par e-mail au chef d’exploitation quand un équipement entre dans les limites géographiques ou en sort.

Chaque équipement a un `deviceId`. Dans ce tutoriel, nous allons suivre un seul équipement dont l’ID unique est `device_1`.

À des fins de clarté, le schéma ci-dessous montre les cinq localisations de l’équipement au fil du temps en commençant par la localisation *Start*, située en dehors des limites géographiques. Dans le cadre de ce tutoriel, la localisation *Start* n’est pas définie, car nous n’interrogerons pas l’appareil à cet emplacement.

Quand nous interrogeons l’[API Get Search Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) avec une localisation d’équipement indiquant la première entrée dans la limite géographique (ou la première sortie), la grille d’événement appelle le point de terminaison d’application logique approprié pour envoyer une notification par e-mail au chef d’exploitation.

Les sections suivantes présentent les requêtes HTTP de l’API GET Geofencing effectuées avec chacune des cinq coordonnées de localisation de l’équipement.

![Mappage de limite géographique dans Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Localisation de l’équipement 1 (47.638237,-122.132483)

1. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande).  Entrez un **Request name** (Nom de demande) pour la demande. Nous allons utiliser le nom *Localisation 1*. Sélectionnez la collection que vous avez créée à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data), puis sélectionnez **Enregistrer**.

2. Sélectionnez la méthode HTTP **GET** dans l’onglet du générateur, puis entrez l’URL suivante. Veillez à remplacer `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement primaire et `{udid}` par l’`udid` que vous avez enregistré à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Cliquez sur le bouton **Envoyer**. Le code GeoJSON suivant s’affiche dans la fenêtre de réponse.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

4. Dans la réponse GeoJSON ci-dessus, la distance négative par rapport à la limite géographique du site principal signifie que l’équipement se trouve à l’intérieur de la limite géographique. La distance positive par rapport à la limite géographique de sous-site signifie que le matériel se trouve en dehors de la limite géographique de sous-site. Étant donné qu’il s’agit de la première fois que cet appareil est localisé dans la limite géographique du site principal, le paramètre `isEventPublished` est défini sur `true` et le chef d’exploitation recevra une notification par e-mail indiquant que l’équipement est entré dans la limite géographique.

### <a name="location-2-4763800-122132531"></a>Localisation 2 (47.63800,-122.132531)

1. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande).  Entrez un **Request name** (Nom de demande) pour la demande. Nous allons utiliser le nom *Localisation 2*. Sélectionnez la collection que vous avez créée à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data), puis sélectionnez **Enregistrer**.

2. Sélectionnez la méthode HTTP **GET** sous l’onglet du générateur, puis entrez l’URL suivante. Veillez à remplacer `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement primaire et `{udid}` par l’`udid` que vous avez enregistré à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Cliquez sur le bouton **Envoyer**. Le code GeoJSON suivant s’affiche dans la fenêtre de réponse :

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. Dans la réponse GeoJSON ci-dessus, l’équipement est resté dans la limite géographique du site principal et n’est pas entré dans la limite géographique du site secondaire. Le paramètre `isEventPublished` est donc défini sur `false`, et le chef d’exploitation ne recevra aucune notification par e-mail.

### <a name="location-3-4763810783315048-12213336020708084"></a>Localisation 3 (47.63810783315048,-122.13336020708084)

1. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande).  Entrez un **Request name** (Nom de demande) pour la demande. Nous allons utiliser le nom *Localisation 3*. Sélectionnez la collection que vous avez créée à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data), puis sélectionnez **Enregistrer**.

2. Sélectionnez la méthode HTTP **GET** sous l’onglet du générateur, puis entrez l’URL suivante. Veillez à remplacer `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement primaire et `{udid}` par l’`udid` que vous avez enregistré à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Cliquez sur le bouton **Envoyer**. Le code GeoJSON suivant s’affiche dans la fenêtre de réponse :

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. Dans la réponse GeoJSON ci-dessus, l’équipement est resté dans la limite géographique du site principal, mais il est entré dans la limite géographique du site secondaire. Le paramètre `isEventPublished` est donc défini sur `true`, et le chef d’exploitation recevra une notification par e-mail indiquant que l’équipement est entré dans une limite géographique.

    >[!NOTE]
    >Si l’équipement est entré dans le site secondaire après les heures d’ouverture, aucun événement n’est publié et le chef d’exploitation ne recevra aucune notification.  

### <a name="location-4-47637988-1221338344"></a>Localisation 4 (47.637988,-122.1338344)

1. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande).  Entrez un **Request name** (Nom de demande) pour la demande. Nous allons utiliser le nom *Localisation 4*. Sélectionnez la collection que vous avez créée à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data), puis sélectionnez **Enregistrer**.

2. Sélectionnez la méthode HTTP **GET** dans l’onglet du générateur, puis entrez l’URL suivante. Veillez à remplacer `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement primaire et `{udid}` par l’`udid` que vous avez enregistré dans la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Cliquez sur le bouton **Envoyer**. Le code GeoJSON suivant s’affiche dans la fenêtre de réponse :

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. Dans la réponse GeoJSON ci-dessus, l’équipement est resté dans la limite géographique du site principal, mais il est sorti de la limite géographique du site secondaire. Toutefois, vous remarquez que la valeur `userTime` est postérieure à la valeur `expiredTime` définie dans les données de limite géographique. Le paramètre `isEventPublished` est donc défini sur `false`, et le chef d’exploitation ne recevra aucune notification par e-mail.

### <a name="location-547637988-1221338344"></a>Localisation 5 (47.637988,-122.1338344)

1. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande).  Entrez un **Request name** (Nom de demande) pour la demande. Nous allons utiliser le nom *Localisation 5*. Sélectionnez la collection que vous avez créée à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data), puis sélectionnez **Enregistrer**.

2. Sélectionnez la méthode HTTP **GET** sous l’onglet du générateur, puis entrez l’URL suivante. Veillez à remplacer `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement primaire et `{udid}` par l’`udid` que vous avez enregistré à la section [Charger les données de geofencing GeoJSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Cliquez sur le bouton **Envoyer**. Le code GeoJSON suivant s’affiche dans la fenêtre de réponse :

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. Dans la réponse GeoJSON ci-dessus, l’équipement est sorti de la limite géographique du site principal. Le paramètre `isEventPublished` est donc défini sur `true`, et le chef d’exploitation recevra une notification par e-mail indiquant que l’équipement est sorti d’une limite géographique.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les types de contenu dans Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Envoyer des notifications par e-mail à l’aide d’Event Grid et de Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Gestionnaires d’événements pris en charge dans Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
