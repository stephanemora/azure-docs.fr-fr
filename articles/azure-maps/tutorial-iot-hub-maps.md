---
title: 'Tutoriel : Implémenter l’analytique spatiale IoT | Microsoft Azure Maps'
description: Intégrez IoT Hub aux API du service Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a49f641561aa7a293628e914c964020145e0ae62
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486419"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Tutoriel : Implémenter l’analytique spatiale IoT avec Azure Maps

Dans un scénario IoT, il est courant de capturer et de suivre les événements pertinents qui se produisent dans l’espace et le temps. Les applications de gestion de flotte, de suivi de ressources, de mobilité et de ville intelligente sont des exemples de scénarios. Ce tutoriel explore un modèle de solution utilisant les API Azure Maps. Les événements pertinents sont capturés par IoT Hub à l’aide du modèle d’abonnement aux événements fourni par Event Grid.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un IoT Hub.
> * Charger la limite géographique dans le service de données Azure Maps à l’aide de l’API Data Upload
> * Créer une fonction dans Azure Functions, en implémentant une logique métier basée sur l’analytique spatiale Azure Maps.
> * Vous abonner aux événements de télémétrie de l’appareil IoT à partir de la fonction Azure par le biais d’Event Grid.
> * Filtrer les événements de télémétrie à l’aide du routage des messages IoT Hub.
> * Créer un compte de stockage pour stocker les données des événements pertinents.
> * Simuler un appareil IoT embarqué.
    

## <a name="use-case"></a>Cas d’utilisation

Cette solution illustre un scénario dans lequel une société de location de voitures prévoit de superviser et de journaliser les événements relatifs à ses voitures de location. Les sociétés de location de voitures louent généralement des voitures dans une région géographique spécifique. Elles doivent suivre les déplacements des voitures pendant qu’elles sont en location. Les instances d’une voiture quittant la région géographique choisie doivent être journalisées. La journalisation des données garantit que les politiques, frais et autres aspects commerciaux seront correctement gérés.

Dans notre cas d’usage, les voitures de location sont équipées d’appareils IoT qui envoient régulièrement des données de télémétrie à Azure IoT Hub. La télémétrie comprend la localisation actuelle et indique si le moteur de la voiture tourne. Le schéma d’emplacement des appareils est conforme au [schéma Plug-and-Play des données géospatiales](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md) IoT. Le schéma de télémétrie des appareils de la voiture de location est semblable à ce qui suit :

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

Utilisons la télémétrie des appareils embarqués pour atteindre notre objectif. Nous souhaitons exécuter des règles de geofencing et répondre chaque fois que nous recevons un événement indiquant que la voiture s’est déplacée. Pour ce faire, nous allons nous abonner aux événements de télémétrie de l’appareil à partir d’IoT Hub par le biais d’Event Grid. 

Il existe plusieurs façons de s’abonner à Event Grid. Dans ce tutoriel, nous utilisons Azure Functions. Azure Functions réagit aux événements publiés dans Event Grid. Il implémente également la logique métier de location de voitures basée sur l’analytique spatiale Azure Maps. 

Le code à l’intérieur de la fonction Azure vérifie si le véhicule a quitté la limite géographique. Si tel est le cas, la fonction Azure collecte des informations supplémentaires telles que l’adresse associée à l’endroit actuel. La fonction implémente également la logique pour stocker des données d’événement significatives dans un stockage d’objets blob de données qui permet de fournir une description des circonstances de l’événement. 

Celles-ci peuvent être utiles à la société de location de voiture et au client de location. Le diagramme suivant vous offre une vue d’ensemble globale du système.

 
  <center>

  ![Vue d’ensemble du système](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

La figure ci-dessous représente la zone de limite géographique mise en surbrillance en bleu. L’itinéraire du véhicule de location est indiqué par une ligne verte.

  ![Itinéraire dans la limite géographique](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Prérequis 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Pour effectuer les étapes de ce tutoriel, vous devez d’abord créer un groupe de ressources dans le portail Azure. Pour créer un groupe de ressources, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Groupes de ressources**.
    
   ![Groupes de ressources](./media/tutorial-iot-hub-maps/resource-group.png)

3. Sous **Groupes de ressources**, sélectionnez **Ajouter**.
    
   ![Ajouter un groupe de ressource](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Entrez les valeurs de propriété suivantes :
    * **Abonnement :** Sélectionnez votre abonnement Azure.
    * **Groupe de ressources :** Entrez « ContosoRental » comme nom de groupe de ressources.
    * **Région :** Sélectionnez une région pour le groupe de ressources.  

    ![Détails du groupe de ressources](./media/tutorial-iot-hub-maps/resource-details.png)

    Sélectionnez **Vérifier + créer**, puis **Créer** dans la page suivante.

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps 

Pour implémenter une logique métier basée sur l’analytique spatiale Azure Maps, nous devons créer un compte Azure Maps dans le groupe de ressources que nous avons créé. Suivez les instructions fournies dans [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) pour créer un abonnement de compte Azure Maps au niveau tarifaire S1. Effectuez les étapes décrites dans [Obtenir la clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account) pour obtenir la clé primaire de votre compte. Pour plus d’informations sur l’authentification dans Azure Maps, voir [Gérer l’authentification dans Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Pour journaliser les données d’événement, nous allons créer un compte **v2storage universel** qui fournit un accès à tous les services de Stockage Azure : objets blob, fichiers, files d’attente, tables et disques.  Nous devrons placer ce compte de stockage dans le groupe de ressources « ContosoRental » pour stocker les données sous forme d’objets blob. Pour créer un compte de stockage, suivez les instructions mentionnées dans [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Nous devrons ensuite créer un conteneur pour stocker les objets blob. Pour ce faire, suivez les étapes ci-dessous :

1. Dans votre « compte de stockage - blob, fichier, table, file d’attente », accédez à Conteneurs.

    ![blobs](./media/tutorial-iot-hub-maps/blobs.png)

2. Cliquez sur le bouton de conteneur situé en haut à gauche, nommez votre conteneur « contoso-rental-logs », puis cliquez sur « OK ».

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Accédez au panneau **Clés d’accès** de votre compte de stockage, puis copiez le « nom du compte de stockage » et la « clé d’accès ». Ils seront nécessaires lors d’une étape ultérieure.

    ![access-keys](./media/tutorial-iot-hub-maps/access-keys.png)


Nous disposons désormais d’un compte de stockage et d’un conteneur pour journaliser les données d’événement. Nous allons à présent créer un hub IoT.

### <a name="create-an-iot-hub"></a>Création d’un IoT Hub

Le hub IoT est un service managé dans le cloud. Il agit en tant que hub de messages central pour la communication bidirectionnelle entre une application IoT et les appareils qu’elle gère. Pour router les messages de télémétrie de l’appareil vers une instance Event Grid, créez un hub IoT dans le groupe de ressources « ContosoRental ». Configurez une intégration de route de messages où nous filtrerons les messages en fonction de l’état du moteur de la voiture. Nous enverrons également les messages de télémétrie de l’appareil à Event Grid chaque fois que la voiture se déplacera.

> [!Note] 
> Les fonctionnalités d’IoT Hub permettant de publier des événements de télémétrie d’appareil sur Event Grid sont en préversion publique. Les fonctionnalités d’évaluation publique sont disponibles dans toutes les régions à l’exception des suivantes : **USA Est, USA Ouest, Europe Ouest, Azure Government, Azure Chine géré par 21Vianet** et **Azure Allemagne**. 

Créez un hub IoT en suivant les étapes mentionnées dans la [section Créer un hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Inscrire un appareil 

Pour se connecter au hub IoT, un appareil doit être inscrit. Pour inscrire un appareil auprès d’IoT Hub, effectuez les étapes suivantes :

1. Dans votre hub IoT, cliquez sur le panneau « Appareils IoT », puis sur « Nouveau ».

    ![add-device](./media/tutorial-iot-hub-maps/add-device.png)

2. Dans la page Créer un appareil, nommez votre appareil IoT, puis cliquez sur « Enregistrer ».
    
    ![register-device](./media/tutorial-iot-hub-maps/register-device.png)

3. Enregistrez la **chaîne de connexion principale** de votre appareil pour l’utiliser dans une étape ultérieure, dans laquelle vous devez remplacer un espace réservé par cette chaîne de connexion.

    ![add-device](./media/tutorial-iot-hub-maps/connectionString.png)

## <a name="upload-geofence"></a>Charger une limite géographique

Nous allons utiliser l’[application Postman](https://www.getpostman.com) pour [charger la limite géographique](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) sur le service Azure Maps à l’aide de l’API de chargement de données Azure Maps. Tout événement se produisant quand la voiture se trouve hors de cette limite géographique sera journalisé.

Ouvrez l’application Postman et suivez les étapes ci-dessous pour charger la limite géographique à l’aide de l’API de chargement des données d’Azure Maps.  

1. Dans l’application Postman, cliquez sur New (Nouveau) | Create new (Créer), puis sélectionnez Request (Requête). Entrez un nom de requête dans Upload geofence data (Charger les données de la limite géographique), sélectionnez la collection ou le dossier où enregistrer la requête, puis cliquez sur Save.

    ![Chargement de limites géographiques à l’aide de Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Sélectionnez la méthode HTTP POST sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    La valeur « geojson » définie pour le paramètre `dataFormat` dans le chemin d’URL représente le format des données en cours de chargement.

3. Cliquez sur **Params**, puis entrez les paires clé/valeur suivantes à utiliser pour l’URL de la requête POST. Remplacez la valeur subscription-key par votre clé Azure Maps.
   
    ![Paramètres clé/valeur dans Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Cliquez sur **Body** (Corps), sélectionnez le format d’entrée **raw** (brut), puis choisissez **JSON (application/text)** (JSON (application/texte)) comme format d’entrée dans la liste déroulante. Ouvrez le fichier de données JSON [ici](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4), puis copiez le fichier JSON dans la section de corps comme données à charger, puis cliquez sur **Envoyer**.
    
    ![publier des données](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Passez en revue les en-têtes de réponse. Quand une requête réussit, l’en-tête **Location** (Emplacement) contient l’URI d’état qui sert à vérifier l’état actuel de la requête de chargement. L’URI d’état sera au format suivant. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copiez votre URI d’état et ajoutez-lui un paramètre `subscription-key`. Affectez la valeur de votre clé d’abonnement de compte Azure Maps au paramètre `subscription-key`. Le format de l’URI d’état doit être similaire à celui ci-dessous et `{Subscription-key}` remplacé par votre clé d’abonnement.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Pour obtenir la valeur `udId`, ouvrez un nouvel onglet dans l’application Postman, sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur), puis effectuez une requête GET sur l’URI d’état. Si le chargement de données réussit, vous recevez un udId dans le corps de la réponse. Copiez la valeur udId pour une utilisation ultérieure.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Nous allons ensuite créer une fonction Azure dans le groupe de ressources « ContosoRental », puis configurer une route des messages dans IoT Hub pour filtrer les messages de télémétrie de l’appareil.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Créer une fonction Azure et ajouter un abonnement Event Grid

Azure Functions est un service de calcul serverless qui nous permet d’exécuter du code à la demande, sans qu’il soit nécessaire de provisionner ou de gérer explicitement l’infrastructure de calcul. Pour en savoir plus sur Azure Functions, consultez la documentation [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

La logique que nous implémentons dans la fonction utilise les données d’emplacement provenant de la télémétrie des appareils embarqués pour évaluer l’état de la limite géographique. Si un véhicule donné sort de la limite géographique, la fonction recueille davantage d’informations (telles que l’adresse du lieu) par le biais de l’[API Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Cette API traduit une coordonnée de lieu donnée en adresse postale compréhensible par l’utilisateur. 

Toutes les informations d’événement pertinentes sont ensuite conservées dans le magasin d’objets blob. L’étape 5 ci-dessous pointe vers le code exécutable qui implémente cette logique. Suivez les étapes ci-dessous pour créer une fonction Azure qui envoie des journaux de données au conteneur d’objets blob dans le compte de stockage d’objets blob et y ajouter un abonnement Event Grid.

1. Dans le tableau de bord du portail Azure, sélectionnez Créer une ressource. Sélectionnez **Calcul** dans la liste des types de ressources disponibles, puis sélectionnez **Application de fonction**.

    ![create-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. Dans la page de création **Function App**, nommez votre application de fonction. Sous **Groupe de ressources**, sélectionnez **Utiliser l’existant**, puis sélectionnez « ContosoRental » dans la liste déroulante. Sélectionnez « .NET Core » comme pile d’exécution. Sous **Hébergement**, pour **Compte de stockage**, sélectionnez le nom du compte de stockage à partir d’une étape précédente. Dans notre étape précédente, nous avons nommé le compte de stockage **v2storage**.  Ensuite, sélectionnez **Vérifier + créer**.
    
    ![create-app](./media/tutorial-iot-hub-maps/rental-app.png)

2. Passez en revue les détails de l’application de fonction, puis sélectionnez « Créer ».

3. Une fois l’application créée, vous devez lui ajouter une fonction. Accédez à l’application de fonction. Cliquez sur **Nouvelle fonction** pour ajouter une fonction, puis choisissez **Dans le portail** comme environnement de développement. Sélectionnez ensuite **Continuer**.

    ![create-function](./media/tutorial-iot-hub-maps/function.png)

4. Choisissez **Autres modèles**, puis cliquez sur **Terminer et afficher les modèles**. 

5. Sélectionnez le modèle avec un **déclencheur Azure Event Grid**. Installez des extensions si vous y êtes invité, nommez la fonction, puis sélectionnez **Créer**.

    ![function-template](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    Le **déclencheur Azure Event Hub** et le **déclencheur Azure Event Grid** ont des icônes similaires. Veillez à sélectionner le **déclencheur Azure Event Grid**.

6. Copiez le [code C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) dans votre fonction.
 
7. Dans le script C#, remplacez les paramètres suivants. Cliquez sur **Enregistrer**. Ne cliquez pas encore sur **Exécuter**.
    * Remplacez **SUBSCRIPTION_KEY** par la clé primaire d’abonnement de votre compte Azure Maps.
    * Remplacez la valeur **UDID** par l’udId de la limite géographique que vous avez chargée. 
    * La fonction **CreateBlobAsync** du script crée un objet blob par événement dans le compte de stockage de données. Remplacez les valeurs **ACCESS_KEY**, **ACCOUNT_NAME** et **STORAGE_CONTAINER_NAME** par la clé d’accès de votre compte de stockage, le nom du compte et le conteneur de stockage de données.

10. Cliquez sur **Ajouter un abonnement Event Grid**.
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Renseignez les détails de l’abonnement : sous **DÉTAILS DES ABONNEMENTS AUX ÉVÉNEMENTS**, nommez votre abonnement d’événement. Pour le schéma d’événement, choisissez « Schéma Event Grid ». Sous **DÉTAILS DE LA RUBRIQUE**, sélectionnez « Comptes Azure IoT Hub » comme Type de rubrique. Choisissez le même abonnement que celui utilisé pour créer le groupe de ressources, puis sélectionnez « ContosoRental » comme « Groupe de ressources ». Choisissez le hub IoT que vous avez créé comme « Ressource ». Choisissez **Télémétrie d’appareil** comme Type d’événement. Une fois ces options choisies, le « Type de rubrique » devient automatiquement « IoT Hub ».

    ![event-grid-subscription](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrer les événements à l’aide du routage des messages IoT Hub

Une fois que vous aurez ajouté un abonnement Event Grid à la fonction Azure, vous pourrez voir une route des messages par défaut vers Event Grid dans le panneau **Routage des messages** d’IoT Hub. Le routage des messages vous permet de router différents types de données vers différents points de terminaison. Vous pouvez par exemple router les messages de télémétrie de l’appareil, les événements du cycle de vie de l’appareil et les événements de changement du jumeau d’appareil. Pour en savoir plus sur le routage des messages IoT Hub, consultez [Utiliser le routage des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

Dans notre exemple de scénario, nous voulons filtrer tous les messages où le véhicule de location se déplace. Pour publier de tels événements de télémétrie d’appareil dans Event Grid, nous allons utiliser la requête de routage pour filtrer les événements pour lesquels la propriété `Engine` a la valeur **« ON »** . Il existe plusieurs façons d’interroger les messages appareil-à-cloud IoT. Pour en savoir plus sur la syntaxe de routage des messages, consultez [Routage des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Pour créer une requête de routage, cliquez sur la route **RouteToEventGrid**, remplacez la **requête de routage** par **« Engine='ON' »** , puis cliquez sur **Enregistrer**. Désormais, IoT Hub publie uniquement les données de télémétrie d’appareil pour lesquelles le paramètre Engine (Moteur) est ON (En marche).

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Envoyer des données de télémétrie à IoT Hub

Une fois que notre fonction Azure est opérationnelle, nous pouvons envoyer les données de télémétrie au hub IoT, qui les route vers Event Grid. Utilisons une application C# pour simuler les données de localisation d’un appareil embarqué d’une voiture de location. Pour exécuter l’application, votre machine de développement doit disposer du SDK .NET Core 2.1.0 ou version ultérieure. Effectuez les étapes ci-dessous pour envoyer les données de télémétrie simulées à IoT Hub.

1. Téléchargez le projet C# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation). 

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

  ![Sortie du terminal](./media/tutorial-iot-hub-maps/terminal.png)

Si vous ouvrez le conteneur de stockage d’objets blob maintenant, vous devez voir quatre objets blob correspondant aux emplacements où le véhicule se trouvait hors de la limite géographique.

![Entrer un objet blob](./media/tutorial-iot-hub-maps/blob.png)

La carte ci-dessous montre quatre points où le véhicule se trouvait hors de la limite géographique, journalisés à intervalles réguliers.

![carte des violations](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Étapes suivantes

Pour explorer les API Azure Maps utilisées dans ce tutoriel, consultez :

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) (Obtenir des adresses de recherche inverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) (Obtenir la limite géographique)

Pour obtenir la liste complète des API REST Azure Maps, consultez :

* [API REST Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Pour en savoir plus sur la préversion d’IoT Plug-and-Play, consultez :

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Pour obtenir la liste des appareils qui sont certifiés Azure pour IoT, visitez :

* [Appareils certifiés Azure](https://catalog.azureiotsolutions.com/)

Pour en savoir plus sur la façon d’envoyer des données de télémétrie appareil-à-cloud, et vice versa, consultez :

* [Envoyer des données de télémétrie depuis un appareil](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
