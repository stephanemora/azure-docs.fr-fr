---
title: Messages cloud-à-appareil avec Azure IoT Hub (Python) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour Python. Vous modifiez une application d’appareil simulé pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110422"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Envoi de messages cloud-à-appareil avec IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md) explique comment créer un hub IoT, y provisionner une identité d’appareil et coder une application d’appareil simulé qui envoie des messages appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce tutoriel est basé sur [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md). Cette rubrique vous explique les procédures suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.

* Recevez des messages cloud-à-appareil sur un appareil.

* À partir du back-end de votre solution, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans le [Guide du développeur IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécuterez deux applications console Python :

* **SimulatedDevice.py**, une version modifiée de l’application créée dans [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md), qui se connecte à votre hub IoT et reçoit des messages cloud-à-appareil.

* **SendCloudToDeviceMessage.py**, qui envoie un message cloud-à-appareil à l’application d’appareil simulé par le biais d’IoT Hub, puis reçoit son accusé de réception.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Assurez-vous que le port 8883 est ouvert dans votre pare-feu. L’exemple d’appareil de cet article utilise le protocole MQTT, lequel communique sur le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Recevoir des messages dans l’application d’appareil simulé

Dans cette section, vous créez une application de console Python pour simuler l’appareil et recevoir des messages cloud-à-appareil à partir de l’IoT Hub.

1. À l’aide d’un éditeur de texte, créez un fichier **SimulatedDevice.py**.

2. Ajoutez les instructions et variables `import` ci-dessous au début du fichier **SimulatedDevice.py** :

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Ajoutez le code suivant au fichier **SimulatedDevice.py**. Remplacez la valeur de l’espace réservé « {deviceConnectionString} » par la chaîne de connexion de l’appareil créée dans le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md) :

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Ajoutez la fonction suivante pour imprimer les messages reçus sur la console :

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Ajoutez le code suivant pour initialiser le client et attendre de recevoir le message cloud-à-appareil :

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Ajoutez la fonction principale suivante :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Enregistrez et fermez le fichier **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

Dans cet article, vous créez un service back-end pour envoyer des messages cloud-à-appareil via l’IoT Hub que vous avez créé dans [Send telemetry from a device to an IoT hub (Envoyer des données de télémétrie d’un appareil à un IoT Hub)](quickstart-send-telemetry-python.md). Pour envoyer des messages cloud-à-appareil, votre service a besoin de l'autorisation de **connexion de service**. Par défaut, chaque IoT Hub est créé avec une stratégie d’accès partagé nommée **service** qui accorde cette autorisation.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil

Dans cette section, vous créez une application de console Python qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md). Vous avez également besoin de la chaîne de connexion de l’IoT Hub que vous avez copiée précédemment dans [Obtention de la chaîne de connexion de l’IoT Hub](#get-the-iot-hub-connection-string).

1. À l’aide d’un éditeur de texte, créez un fichier **SendCloudToDeviceMessage.py**.

2. Ajoutez les instructions et variables `import` ci-dessous au début du fichier **SendCloudToDeviceMessage.py** :

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Ajoutez le code suivant au fichier **SendCloudToDeviceMessage.py**. Remplacez les valeurs d’espace réservé "{iot hub connection string}" et "{device id}" par la chaîne de connexion de l’IoT Hub et l’identité d'appareil que vous avez notées précédemment :

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Ajoutez la fonction suivante pour imprimer les messages de commentaires sur la console :

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Ajoutez le code suivant pour envoyer un message à votre appareil et gérer le message de commentaires lorsque l’appareil accuse réception d’un message cloud-à-appareil :

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Ajoutez la fonction principale suivante :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Enregistrez et fermez le fichier **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. Ouvrez une invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Device pour Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. À l’invite de commandes, exécutez la commande suivante pour écouter les messages cloud-à-appareil :

    ```shell
    python SimulatedDevice.py
    ```

    ![Exécution de l’application de périphérique simulé](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Ouvrez une nouvelle invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Service pour Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. À l’invite de commandes, exécutez la commande suivante pour envoyer un message cloud-à-appareil, puis attendez de recevoir les commentaires de message :

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Exécutez l’application pour envoyer la commande cloud-à-appareil](./media/iot-hub-python-python-c2d/send-command.png)

5. Notez le message reçu par l’appareil.

    ![Message reçu](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateur de solution de supervision à distance Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur d’IoT Hub](iot-hub-devguide.md).
