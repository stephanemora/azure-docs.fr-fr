---
title: Messages cloud-à-appareil avec Azure IoT Hub (Python) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour Python. Vous modifiez une application d’appareil simulé pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: 7ac668bdbc3698be3ed2aa50a428cef84e68369a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272868"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Envoi de messages cloud-à-appareil avec IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Présentation
Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Le [bien démarrer avec IoT Hub](quickstart-send-telemetry-python.md) démarrage rapide montre comment créer un IoT hub, approvisionner une identité d’appareil et coder une application d’appareil simulé qui envoie des messages appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel s’appuie sur l’article [Prise en main d’IoT Hub](quickstart-send-telemetry-python.md). Cette rubrique vous explique les procédures suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.

* Recevez des messages cloud-à-appareil sur un appareil.

* À partir du serveur principal de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez plus d’informations sur les messages cloud-à-appareil dans le [guide du développeur IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécuterez deux applications console Python :

* **SimulatedDevice.py**, une version modifiée de l’application créée dans [Prise en main d’IoT Hub](quickstart-send-telemetry-python.md)qui se connecte à votre IoT Hub et reçoit les messages entre cloud et appareils.

* **SendCloudToDeviceMessage.py**, qui envoie un message cloud-à-appareil à l’application pour appareil simulée par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT](https://www.azure.com/develop/iot).
>

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Python 2.x ou 3.x](https://www.python.org/downloads/). Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion des packages Python](https://pip.pypa.io/en/stable/installing/).

* Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) pour autoriser l’utilisation de DLL natives de Python.

* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

> [!NOTE]
> Les packages *pip* pour `azure-iothub-service-client` et `azure-iothub-device-client` sont actuellement disponibles uniquement pour les systèmes d’exploitation Windows. Pour Linux/Mac OS, reportez-vous aux sections spécifiques de Linux et Mac OS sur le [préparer votre environnement de développement pour Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) valider.
>

## <a name="receive-messages-in-the-simulated-device-app"></a>Recevoir des messages dans l’application d’appareil simulé

Dans cette section, vous créez une application de console Python pour simuler l’appareil et recevoir des messages cloud-à-appareil à partir de l’IoT Hub.

1. À l’aide d’un éditeur de texte, créez un fichier **SimulatedDevice.py**.

2. Ajoutez les instructions et variables `import` ci-dessous au début du fichier **SimulatedDevice.py** :

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. Ajoutez le code suivant au fichier **SimulatedDevice.py**. Remplacez la valeur d’espace réservé « {deviceConnectionString} » par la chaîne de connexion pour l’appareil que vous avez créé dans le [bien démarrer avec IoT Hub](quickstart-send-telemetry-python.md) Guide de démarrage rapide :

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Ajoutez la fonction suivante pour imprimer les messages reçus sur la console :

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. Ajoutez le code suivant pour initialiser le client et attendre de recevoir le message cloud-à-appareil :

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. Ajoutez la fonction principale suivante :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Enregistrez et fermez le fichier **SimulatedDevice.py**.

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil

Dans cette section, vous créez une application de console Python qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le [bien démarrer avec IoT Hub](quickstart-send-telemetry-python.md) Guide de démarrage rapide. Vous avez également besoin de la chaîne de connexion pour votre hub que vous trouverez dans le [Portail Azure](https://portal.azure.com).

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

3. Ajoutez le code suivant au fichier **SendCloudToDeviceMessage.py**. Remplacez la valeur d’espace réservé « {IoTHubConnectionString} » par la chaîne de connexion IoT Hub pour le hub créé dans le [bien démarrer avec IoT Hub](quickstart-send-telemetry-python.md) Guide de démarrage rapide. Remplacez l’espace réservé « {deviceId} » avec l’ID de l’appareil que vous avez ajouté dans le [bien démarrer avec IoT Hub](quickstart-send-telemetry-python.md) Guide de démarrage rapide :

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

    ```
    pip install azure-iothub-device-client
    ```

2. À l’invite de commandes, exécutez la commande suivante pour écouter les messages cloud-à-appareil :

    ```shell
    python SimulatedDevice.py
    ```

    ![Exécution de l’application de périphérique simulé](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Ouvrez une nouvelle invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Service pour Python**.

    ```
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
