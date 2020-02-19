---
title: Bien démarrer avec les jumeaux d’appareils Azure IoT Hub (Python) | Microsoft Docs
description: Guide d’utilisation des jumeaux d’appareils Azure IoT Hub pour ajouter des balises, puis utiliser une requête IoT Hub. Utilisez les kits Azure IoT SDK pour Python afin d’implémenter l’application d’appareil simulé et une application de service qui ajoute les balises et exécute la requête IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: a6210c4672042801350e56ef6c8e8a2c02420a81
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110394"
---
# <a name="get-started-with-device-twins-python"></a>Bien démarrer avec les jumeaux d’appareils (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console Python :

* **AddTagsAndQuery.py**, application Python qui ajoute des balises et interroge des jumeaux d’appareils.

* **ReportConnectivity.py**, application Python qui simule un appareil se connectant à votre hub IoT avec l’identité d’appareil créée précédemment, et signale son état de connectivité.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Assurez-vous que le port 8883 est ouvert dans votre pare-feu. L’exemple d’appareil de cet article utilise le protocole MQTT, lequel communique sur le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous créez une application console Python qui ajoute des métadonnées d’emplacement au jumeau d’appareil associé à votre **{ID d’appareil}** . Elle interroge ensuite les jumeaux d’appareils stockés dans le hub IoT en sélectionnant les appareils situés à Redmond, puis ceux qui signalent une connexion mobile.

1. Dans votre répertoire de travail, ouvrez une invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Service pour Python**.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Le package PIP pour azure-iothub-service-client n’est actuellement disponible que pour le système d’exploitation Windows. Pour Linux/Mac OS, reportez-vous aux sections spécifiques de Linux et Mac OS de la publication [Prepare your development environment for Python](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) (Préparer votre environnement de développement pour Python).
   >

2. À l’aide d’un éditeur de texte, créez un fichier **AddTagsAndQuery.py**.

3. Ajoutez le code suivant pour importer les modules requis à partir du Kit de développement logiciel (SDK) de service :

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Ajoutez le code ci-dessous. Remplacez `[IoTHub Connection String]` par la chaîne de connexion de l’IoT Hub que vous avez copiée dans [Obtention de la chaîne de connexion de l’IoT Hub](#get-the-iot-hub-connection-string). Remplacez `[Device Id]` par l’ID d’appareil que vous avez inscrit à l’étape [ Inscrire un nouvel appareil dans le hub IoT](#register-a-new-device-in-the-iot-hub).
  
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

    ![Première requête affichant tous les appareils à Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

Dans la section suivante, vous allez créer une application d’appareil qui transmet les informations de connectivité et modifie le résultat de la requête de la section précédente.

## <a name="create-the-device-app"></a>Créer l’application pour appareil

Dans cette section, vous allez créer une application console Python qui se connecte à votre hub en tant que votre **{ID d’appareil}** , puis met à jour les propriétés signalées de son jumeau d’appareil afin qu’elles contiennent les informations indiquant qu’il est connecté par le biais d’un réseau cellulaire.

1. À partir d’une invite de commandes dans votre répertoire de travail, installez le **Kit SDK Azure IoT Hub Device pour Python** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. À l’aide d’un éditeur de texte, créez un fichier **ReportConnectivity.py**.

3. Ajoutez le code suivant pour importer les modules requis à partir du Kit de développement logiciel (SDK) d’appareil :

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Ajoutez le code ci-dessous. Remplacez la valeur d’espace réservé `[IoTHub Device Connection String]` par la chaîne de connexion de l’appareil que vous avez copiée dans [Inscrire un nouvel appareil dans le hub IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Ajoutez le code suivant au fichier **ReportConnectivity.py** pour implémenter la fonctionnalité de jumeaux d’appareils :

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    L’objet **Client** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareil à partir de l’appareil. Le code précédent, après avoir initialisé l’objet **Client**, récupère le jumeau de votre appareil, puis met à jour sa propriété signalée avec les informations de connectivité.

6. Ajoutez le code suivant à la fin de **ReportConnectivity.py** pour implémenter la fonction **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Exécutez l’application d’appareil :

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Vous devez voir un message confirmant que les jumeaux d’appareils ont été mis à jour.

    ![jumeaux mis à jour](./media/iot-hub-python-twin-getstarted/device-1.png)

8. À présent que l’appareil a signalé ses informations de connectivité, il doit apparaître dans les deux requêtes. Revenez en arrière et réexécutez les requêtes :

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Cette fois, votre **{ID d’appareil}** doit apparaître dans les résultats des deux requêtes.

    ![deuxième requête](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez ajouté des métadonnées d’appareil en tant que balises à partir d’une application principale et écrit une application pour appareil simulée pour signaler des informations de connectivité d’appareil dans le jumeau d’appareil. Vous avez également appris à interroger ces informations à l’aide du Registre.

Utilisez les ressources suivantes :

* Envoyez des données de télémétrie à partir d’appareils en suivant le didacticiel [Bien démarrer avec IoT Hub](quickstart-send-telemetry-python.md).

* Pour savoir comment configurer des appareils à l’aide des propriétés de jumeau d’appareil souhaitées, suivez le didacticiel [Utiliser des propriétés souhaitées pour configurer des appareils](tutorial-device-twins.md).

* Pour savoir comment contrôler les appareils de façon interactive (par exemple pour mettre en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), suivez le didacticiel [Utiliser des méthodes directes](quickstart-control-device-python.md).
