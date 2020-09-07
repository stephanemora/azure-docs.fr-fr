---
title: 'Tutoriel : Implémenter l’analytique spatiale IoT avec Microsoft Azure Maps'
description: Intégrez IoT Hub aux API du service Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4150464b5c59b631afea0c788b1e351dee5185f9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299381"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutoriel : Implémenter l’analytique spatiale IoT avec Azure Maps

Dans un scénario IoT, il est courant de capturer et de suivre les événements pertinents qui se produisent dans l’espace et le temps. Les applications de gestion de flotte, de suivi de ressources, de mobilité et de ville intelligente sont des exemples de scénarios. Ce tutoriel explore un modèle de solution qui effectue le suivi des déplacements d’une voiture de location à l’aide des API Azure Maps.

Dans ce tutoriel vous allez :

> [!div class="checklist"]
> * Créer un compte Stockage Azure pour journaliser les données de suivi.
> * Charger une limite géographique dans le service de données Azure Maps à l’aide de l’API Data Upload.
> * Créer un IoT Hub et inscrire un appareil.
> * Créer une fonction dans Azure Functions, en implémentant une logique métier basée sur l’analytique spatiale Azure Maps.
> * Vous abonner aux événements de télémétrie de l’appareil IoT à partir de la fonction Azure par le biais d’Event Grid.
> * Filtrer les événements de télémétrie à l’aide du routage des messages IoT Hub.

## <a name="prerequisites"></a>Prérequis

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. [Créez un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Obtenir une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement. Pour plus d’informations sur l’authentification dans Azure Maps, voir [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md).

4. [Créez un groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). Dans ce tutoriel, nous allons nommer notre groupe de ressources *ContosoRental*, mais vous pouvez choisir le nom de votre choix.

5. Téléchargez le [projet C# rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Ce tutoriel utilise l’application [Postman](https://www.postman.com/), mais vous pouvez choisir un autre environnement de développement d’API.

## <a name="use-case-rental-car-tracking"></a>Cas d’usage : Suivi de voiture de location

Ce tutoriel présente le scénario suivant : Une société de location de voitures souhaite journaliser les informations sur l’emplacement, les distances parcourues et l’état de fonctionnement de ses voitures de location. En outre, la société souhaite stocker ces informations à chaque fois qu’une voiture quitte la région géographique autorisée correcte.

Dans notre cas d’usage, les voitures de location sont équipées d’appareils IoT qui envoient régulièrement des données de télémétrie à Azure IoT Hub. La télémétrie comprend la localisation actuelle et indique si le moteur de la voiture tourne. Le schéma d’emplacement des appareils est conforme au [schéma Plug-and-Play des données géospatiales](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md) IoT. Le schéma de télémétrie des appareils de la voiture de location est semblable au code JSON suivant :

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

Dans ce tutoriel, nous ne faisons le suivi que d’un seul véhicule. Après avoir configuré les services Azure, vous devez télécharger le [projet C# rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) pour exécuter le simulateur de véhicule. L’ensemble du processus, de l’événement à l’exécution de la fonction, est résumé dans les étapes suivantes :

1. L’appareil embarqué envoie des données de télémétrie à IoT Hub.

2. Si le moteur de la voiture est allumé, IoT Hub publie les données de télémétrie sur Event Grid.

3. Une fonction Azure est déclenchée en raison de son abonnement aux événements de télémétrie de l’appareil.

4. La fonction journalise les coordonnées de l’emplacement de l’appareil du véhicule, l’heure de l’événement et l’ID de l’appareil. Elle utilise ensuite [l’API Get Spatial Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) pour déterminer si la voiture est allée à l’extérieur de la limite géographique. Si c’est le cas, la fonction stocke les données d’emplacement reçues de l’événement dans notre conteneur blob. En outre, notre fonction interroge [Recherche d’adresse inversée](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pour convertir l’emplacement des coordonnée en adresse postale et stocker celle-ci avec les autres données d’emplacement de l’appareil.

Le diagramme suivant vous offre une vue d’ensemble globale du système.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Vue d’ensemble du système":::

La figure ci-dessous met en évidence la zone de limite géographique en bleu. L’itinéraire du véhicule de location est indiqué par une ligne verte.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Itinéraire dans la limite géographique":::

## <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

Pour stocker les données de suivi des infractions de la voiture, nous allons créer un [compte de stockage v2 à usage général](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) dans votre groupe de ressources. Si vous n’avez pas créé de groupe de ressources, suivez les instructions de la section [Créer un groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). Dans ce tutoriel, nous allons nommer notre groupe de ressources *ContosoRental*.

Pour créer un compte de stockage, suivez les instructions de la section [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). Dans ce tutoriel, nous allons nommer le compte de stockage *contosorentalstorage*, mais vous pouvez le nommer comme vous le souhaitez.

Une fois votre compte de stockage créé, nous devons créer un conteneur pour stocker les données de journalisation.

1. Accédez au compte de stockage que vous venez de créer. Cliquez sur le lien **Conteneurs** dans la section Essentials.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Conteneurs pour le stockage blob":::

2. Cliquez sur le bouton **+ Conteneur** dans le coin supérieur gauche. Un panneau s’affiche sur le côté droit du navigateur. Nommez votre conteneur *contoso-rental-logs* et cliquez sur **Créer**.

     Créer un :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="conteneur d’objets blob":::

3. Accédez au panneau **Clés d’accès** de votre compte de stockage et copiez le **nom du compte de stockage** et la valeur **Clé** dans la section **key1**. Nous aurons besoin de ces deux valeurs dans la section [Créer une fonction Azure et ajouter un abonnement Event Grid](#create-an-azure-function-and-add-an-event-grid-subscription).

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Copier le nom du compte de stockage et la clé":::

## <a name="upload-a-geofence"></a>Charger une limite géographique

Nous allons maintenant utiliser [l’application Postman](https://www.getpostman.com) pour [charger la limite géographique](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) dans le service Azure Maps. La limite géographique définit la zone géographique autorisée pour notre véhicule de location.  Nous utiliserons la limite géographique dans notre fonction Azure pour déterminer si une voiture est allée en dehors de la zone de limite géographique.

Ouvrez l’application Postman et suivez les étapes ci-dessous pour charger la limite géographique à l’aide de l’API Azure Maps Data Upload.  

1. Ouvrez l’application Postman. En haut de l'application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Collection**.  Nommez la collection puis sélectionnez le bouton **Create** (Créer).

2. Pour créer la demande, sélectionnez **New** à nouveau. Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande). Entrez un **Request name** (Nom de demande) pour la demande. Sélectionnez la collection que vous avez créée à l’étape précédente, puis sélectionnez **Enregistrer**.

3. Sélectionnez la méthode HTTP **POST** sous l’onglet du générateur, puis entrez l’URL suivante pour charger la limite géographique dans l’API Data Upload. Remplacez `{subscription-key}` par la clé de votre abonnement principal.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    La valeur « geojson » définie pour le paramètre `dataFormat` dans le chemin d’URL représente le format des données en cours de chargement.

4. Cliquez sur **Body** (Corps), sélectionnez le format d’entrée **Raw** (Brut), puis choisissez **JSON** comme format d’entrée dans la liste déroulante. Ouvrez le fichier de données JSON [ici](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) et copiez le code JSON dans la section du corps. Cliquez sur **Envoyer**.

5. Cliquez sur le bouton bleu **Envoyer**, puis attendez que la requête soit traitée. Une fois la requête terminée, accédez à l’onglet **En-têtes** de la réponse. Copiez la valeur de la **Emplacement**, qui est l’URL `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Pour vérifier l’état de l’appel d’API, créez une requête HTTP **GET** sur `status URL`. Vous devez ajouter votre clé d’abonnement principale à l’URL pour l’authentification. La requête **GET** doit ressembler à l’URL suivante :

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Créer un hub IoT Azure

Azure IoT Hub permet une communication bidirectionnelle sécurisée et fiable entre une application IoT et les appareils qu’elle gère.  Dans notre scénario, nous voulons obtenir des informations à partir de l’appareil embarqué dans le véhicule pour déterminer l’emplacement de la voiture de location. Dans cette section, nous allons créer un IoT Hub dans le groupe de ressources *ContosoRental*. IoT Hub est chargé de la publication des événements de télémétrie de l’appareil.

> [!NOTE]
> La fonctionnalité d’IoT Hub permettant de publier des événements de télémétrie d’appareil sur Event Grid est en préversion publique. Les fonctionnalités d’évaluation publique sont disponibles dans toutes les régions à l’exception des suivantes : **USA Est, USA Ouest, Europe Ouest, Azure Government, Azure Chine géré par 21Vianet** et **Azure Allemagne**.

Pour créer un IoT Hub dans le groupe de ressources *ContosoRental*, suivez les étapes décrites dans [Créer un IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-iot-hub"></a>Inscrire un appareil dans IoT Hub

Les appareils ne peuvent pas se connecter à IoT Hub, sauf s’ils sont inscrits dans le registre des identités IoT Hub. Dans notre scénario, nous allons créer un seul appareil portant le nom *InVehicleDevice*. Pour créer et inscrire l’appareil dans IoT Hub, suivez les étapes décrites dans [Inscrire un nouvel appareil dans IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Veillez à copier la **chaîne de connexion principale** de votre appareil, car nous allons l’utiliser dans une étape ultérieure.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Créer une fonction Azure et ajouter un abonnement Event Grid

Azure Functions est un service de calcul serverless qui vous permet d’exécuter de petites portions de code (« fonctions »), sans qu’il soit nécessaire de provisionner ou de gérer explicitement l’infrastructure de calcul. Pour en savoir plus sur Azure Functions, consultez la documentation [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

Une fonction est « déclenchée » par un événement spécifique. Dans notre scénario, nous allons créer une fonction qui est déclenchée par un déclencheur Event Grid. Nous créons la relation entre le déclencheur et la fonction en créant un abonnement à un événement pour les événements de télémétrie de l’appareil IoT Hub. Lorsqu’un événement de télémétrie de l’appareil se produit, notre fonction est appelée comme point de terminaison et reçoit les données pertinentes pour [l’appareil que nous avons précédemment inscrit dans IoT Hub](#register-a-device-in-iot-hub).

Le code de script C# que notre fonction contient peut être consulté [ici](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Nous allons maintenant configurer notre fonction Azure.

1. Dans le tableau de bord du portail Azure, cliquez sur **Créer une ressource**. Saisissez **Function App** dans la zone de texte de recherche. Cliquez sur **Function App**. Cliquez sur **Créer**.

2. Dans la page de création **Function App**, nommez votre application de fonction. Sous **Groupe de ressources**, sélectionnez *ContosoRental* dans la liste déroulante.  Sélectionnez *.NET Core* comme **pile d’exécution**. Cliquez sur **Suivant : Hébergement >** en bas de la page.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Créer une application de fonction":::

3. Pour **Compte de stockage**, sélectionnez le compte de stockage que vous avez créé dans [Créer un compte de stockage Azure](#create-an-azure-storage-account). Cliquez sur **Vérifier + créer**.

4. Vérifiez les détails de l’application de fonction et cliquez sur **Créer**.

5. Une fois l’application créée, nous allons y ajouter une fonction. Accédez à l’application de fonction. Cliquez sur le panneau **Fonctions**. Cliquez sur **+ Ajouter** en haut de la page. Le panneau de modèle de fonction s’affiche. Faites défiler le panneau vers le bas. Cliquez sur **Déclencheur Azure Event Grid**.

     >[!WARNING]
    > Les modèles **Azure Event Hub Trigger** (Déclencheur Azure Event Hub) et **Azure Event Grid Trigger** (Déclencheur Azure Event Grid) ont des noms similaires. Veillez à cliquer sur le modèle **Azure Event Grid Trigger** (Déclencheur Azure Event Grid).

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Créer une fonction":::

6. Donnez un nom à la fonction. Dans ce tutoriel, nous allons utiliser le nom *GetGeoFunction*, mais vous pouvez utiliser le nom de votre choix. Cliquez sur **Créer une fonction**.

7. Cliquez sur le panneau **Code + test** dans le menu de gauche. Copiez et collez le [Script C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) dans la fenêtre de code.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Copier/coller du code dans la fenêtre de fonction":::

8. Dans le code C#, remplacez les paramètres suivants. Cliquez sur **Enregistrer**. Ne cliquez pas encore sur **Tester/exécuter**.
    * Remplacez **SUBSCRIPTION_KEY** par la clé principale d’abonnement de votre compte Azure Maps.
    * Remplacez **UDID** par `udid` de la limite géographique que vous avez chargée dans [Charger une limite géographique](#upload-a-geofence).
    * La fonction **CreateBlobAsync** du script crée un objet blob par événement dans le compte de stockage de données. Remplacez les valeurs **ACCESS_KEY**, **ACCOUNT_NAME** et **STORAGE_CONTAINER_NAME** par la clé d’accès de votre compte de stockage, le nom du compte et le conteneur de stockage de données. Ces valeurs ont été générées lors de la création de votre compte de stockage dans la section [Créer un compte de stockage Azure](#create-an-azure-storage-account).

9. Cliquez sur le panneau **Intégration** dans le menu de gauche. Cliquez sur **Déclencheur Event Grid** dans le diagramme. Saisissez un nom pour le déclencheur, comme *eventCarTelemetry*, puis cliquez sur **Créer un abonnement Event Grid**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Ajouter un abonnement à un événement":::

10. Renseignez les détails de l’abonnement. Donnez un nom à l’abonnement à l’événement. Pour *Schéma d’événement*, sélectionnez *Schéma Event Grid*. Pour **Types de rubrique**, sélectionnez *Comptes Azure IoT Hub*. Pour **Groupe de ressources**, sélectionnez le groupe de ressources que vous avez créé au début de ce tutoriel. Pour **Ressource**, sélectionnez l’IoT Hub que vous avez créé dans [Créer un Azure IoT Hub](#create-an-azure-iot-hub). Pour **Filtrer sur les types d’événements**, sélectionnez *Télémétrie de l’appareil*. Une fois ces options choisies, le **Type de rubrique** devient *IoT Hub*. Pour **Nom de rubrique système**, vous pouvez utiliser le même nom que celui de votre ressource.  Enfin, cliquez sur **Sélectionner un point de terminaison** dans la section **Détails du point de terminaison**. Acceptez tous les paramètres, puis cliquez sur **Confirmer la sélection**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Créer un abonnement aux événements":::

11. Passez vos paramètres en revue. Assurez-vous que le point de terminaison spécifie la fonction que vous avez créée au début de cette section. Cliquez sur **Créer**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Confirmation de création d’un abonnement à l’événement":::

12. Vous êtes à présent de retour dans le panneau **Modifier le déclencheur**. Cliquez sur **Enregistrer**.

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrer les événements à l’aide du routage des messages IoT Hub

Lorsque vous ajoutez un abonnement Event Grid à la fonction Azure, un itinéraire de messagerie est automatiquement créé dans l’IoT Hub spécifié. Le routage des messages vous permet d’acheminer différents types de données vers différents points de terminaison. Vous pouvez par exemple router les messages de télémétrie de l’appareil, les événements du cycle de vie de l’appareil et les événements de changement du jumeau d’appareil. Pour en savoir plus sur le routage des messages IoT Hub, consultez [Utiliser le routage des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Routage des messages dans IoT Hub":::

Dans notre exemple de scénario, nous ne voulons recevoir des messages que lorsque la voiture de location se déplace. Nous allons créer une requête de routage pour filtrer les événements dont la propriété `Engine` est égale à **« ON »** . Pour créer une requête de routage, cliquez sur la route **RouteToEventGrid**, remplacez la **requête de routage** par **« Engine='ON' »**, puis cliquez sur **Enregistrer**. Désormais, IoT Hub publie uniquement les données de télémétrie d’appareil pour lesquelles le paramètre Engine (Moteur) est ON (En marche).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Filtrer le routage des messages":::

>[!TIP]
>Il existe plusieurs façons d’interroger les messages appareil-à-cloud IoT. Pour en savoir plus sur la syntaxe de routage des messages, consultez [Routage des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Envoyer des données de télémétrie à IoT Hub

Une fois que notre fonction Azure est opérationnelle, nous pouvons envoyer les données de télémétrie à l’IoT Hub, qui les route vers Event Grid. Utilisons une application C# pour simuler les données de localisation d’un appareil embarqué d’une voiture de location. Pour exécuter l’application, votre machine de développement doit disposer du SDK .NET Core 2.1.0 ou version ultérieure. Effectuez les étapes ci-dessous pour envoyer les données de télémétrie simulées à IoT Hub.

1. Si ce n’est déjà fait, téléchargez le projet C# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

2. Ouvrez le fichier simulatedCar.cs dans un éditeur de texte de votre choix, remplacez la valeur de `connectionString` par celle que vous avez enregistrée lors de l’inscription de l’appareil, puis enregistrez les changements apportés au fichier.

3. Vérifiez que .NET Core est installé sur votre ordinateur. Dans la fenêtre de votre terminal local, accédez au dossier racine du projet C#, puis exécutez la commande suivante pour installer les packages nécessaires pour l’application d’appareil simulé :

    ```cmd/sh
    dotnet restore
    ```

4. Dans le même terminal, exécutez la commande suivante pour générer et exécuter l’application de simulation de voiture de location :

    ```cmd/sh
    dotnet run
    ```

  Votre terminal local doit ressembler à celui présenté ci-dessous.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Sortie du terminal":::

Si vous ouvrez le conteneur de stockage blob maintenant, vous pouvez voir quatre objets blob correspondant aux emplacements où le véhicule se trouvait hors de la limite géographique.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Afficher les objets blob dans le conteneur":::

La carte ci-dessous montre quatre points d’emplacement du véhicule en dehors de la limite géographique. Chaque emplacement a été journalisé à intervalles réguliers.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Carte des violations":::

## <a name="explore-azure-maps-and-iot"></a>Explorer Azure Maps et IoT

Pour explorer les API Azure Maps utilisées dans ce tutoriel, consultez :

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) (Obtenir la limite géographique)

Pour obtenir la liste complète des API REST Azure Maps, consultez :

* [API REST Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Pour en savoir plus sur la préversion d’IoT Plug-and-Play, consultez :

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Pour obtenir la liste des appareils qui sont certifiés Azure pour IoT, visitez :

* [Appareils certifiés Azure](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon d’envoyer des données de télémétrie appareil-à-cloud, et vice versa, consultez :

> [!div class="nextstepaction"]
> [Envoyer des données de télémétrie depuis un appareil](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
