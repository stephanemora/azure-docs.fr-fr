---
title: Bien démarrer avec les jumeaux d’appareils Azure IoT Hub (Python) | Microsoft Docs
description: Guide d’utilisation des jumeaux d’appareils Azure IoT Hub pour ajouter des balises, puis utiliser une requête IoT Hub. Utilisez les kits Azure IoT SDK pour Python afin d’implémenter l’application d’appareil simulé et une application de service qui ajoute les balises et exécute la requête IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 62385f4bd07f4b80dc3d571d409e16c7e0dca205
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667857"
---
# <a name="get-started-with-device-twins-python"></a>Bien démarrer avec les jumeaux d’appareils (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console Python :

* **AddTagsAndQuery.py**, application Python qui ajoute des balises et interroge des jumeaux d’appareils.

* **ReportConnectivity.py**, application Python qui simule un appareil se connectant à votre hub IoT avec l’identité d’appareil créée précédemment, et signale son état de connectivité.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Voici les instructions d’installation des prérequis.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous créez une application console Python qui ajoute des métadonnées d’emplacement au jumeau d’appareil associé à votre **{ID d’appareil}** . Elle interroge ensuite les jumeaux d’appareils stockés dans le hub IoT en sélectionnant les appareils situés à Redmond, puis ceux qui signalent une connexion mobile.

1. Ouvrez une invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Service pour Python**. Fermez l’invite de commandes après avoir installé le Kit de développement logiciel (SDK).

   ```
   pip install azure-iothub-service-client
   ```

2. À l’aide d’un éditeur de texte, créez un fichier **AddTagsAndQuery.py**.

3. Ajoutez le code suivant pour importer les modules requis à partir du Kit de développement logiciel (SDK) de service :

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Ajoutez le code suivant, en remplaçant la valeur d’espace réservé pour `[IoTHub Connection String]` et `[Device Id]` par la chaîne de connexion pour le hub IoT et l’ID d’appareil créés dans les sections précédentes.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Ajoutez le code suivant au fichier **AddTagsAndQuery.py** :

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    L’objet **Registry** expose toutes les méthodes requises pour interagir avec des représentations d’appareil à partir du service. Le code initialise tout d’abord l’objet **Registry**, il met à jour le jumeau d’appareil pour **deviceId**, puis il exécute deux requêtes. La première sélectionne uniquement les jumeaux d’appareils situés dans l’usine **Redmond43** et la seconde affine la requête pour sélectionner uniquement les appareils qui sont également connectés par le biais d’un réseau cellulaire.

6. Ajoutez le code suivant à la fin de **AddTagsAndQuery.py** pour implémenter la fonction **iothub_service_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Exécutez l’application avec :

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Vous devriez voir un appareil dans les résultats de la requête demandant tous les appareils situés à **Redmond43**, et aucun pour la requête limitant les résultats aux appareils utilisant un réseau cellulaire.

    ![Première requête affichant tous les appareils à Redmond](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

Dans la section suivante, vous allez créer une application d’appareil qui transmet les informations de connectivité et modifie le résultat de la requête de la section précédente.

## <a name="create-the-device-app"></a>Créer l’application pour appareil

Dans cette section, vous allez créer une application console Python qui se connecte à votre hub en tant que votre **{ID d’appareil}** , puis met à jour les propriétés signalées de son jumeau d’appareil afin qu’elles contiennent les informations indiquant qu’il est connecté par le biais d’un réseau cellulaire.

1. Ouvrez une invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Service pour Python**. Fermez l’invite de commandes après avoir installé le Kit de développement logiciel (SDK).

    ```
    pip install azure-iothub-device-client
    ```

2. À l’aide d’un éditeur de texte, créez un fichier **ReportConnectivity.py**.

3. Ajoutez le code suivant pour importer les modules requis à partir du Kit de développement logiciel (SDK) de service :

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Ajoutez le code suivant, en remplaçant la valeur d’espace réservé pour `[IoTHub Device Connection String]` par la chaîne de connexion pour l’appareil IoT Hub créé dans les sections précédentes.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Ajoutez le code suivant au fichier **ReportConnectivity.py** pour implémenter la fonctionnalité de jumeaux d’appareils :

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    L’objet **Client** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareil à partir de l’appareil. Le code précédent, après avoir initialisé l’objet **Client**, récupère le jumeau de votre appareil, puis met à jour sa propriété signalée avec les informations de connectivité.

6. Ajoutez le code suivant à la fin de **ReportConnectivity.py** pour implémenter la fonction **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Exécutez l’application d’appareil :

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Vous devez voir un message confirmant que les jumeaux d’appareils ont été mis à jour.

    ![jumeaux mis à jour](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. À présent que l’appareil a signalé ses informations de connectivité, il doit apparaître dans les deux requêtes. Revenez en arrière et réexécutez les requêtes :

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Cette fois, votre **{ID d’appareil}** doit apparaître dans les résultats des deux requêtes.

    ![deuxième requête](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau hub IoT dans le portail Azure, puis créé une identité d’appareil dans le registre des identités du hub IoT. Vous avez ajouté des métadonnées d’appareil en tant que balises à partir d’une application principale et écrit une application pour appareil simulée pour signaler des informations de connectivité d’appareil dans le jumeau d’appareil. Vous avez également appris à interroger ces informations à l’aide du Registre.

Utilisez les ressources suivantes :

* Envoyez des données de télémétrie à partir d’appareils en suivant le didacticiel [Bien démarrer avec IoT Hub](quickstart-send-telemetry-python.md).

* Pour savoir comment configurer des appareils à l’aide des propriétés de jumeau d’appareil souhaitées, suivez le didacticiel [Utiliser des propriétés souhaitées pour configurer des appareils](tutorial-device-twins.md).

* Pour savoir comment contrôler les appareils de façon interactive (par exemple pour mettre en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), suivez le didacticiel [Utiliser des méthodes directes](quickstart-control-device-python.md).