---
title: Tutoriel – Connecter une application cliente Python générique à Azure IoT Central | Microsoft Docs
description: Ce tutoriel vous explique comment, en tant que développeur d’appareils, connecter un appareil exécutant une application cliente Python à votre application Azure IoT Central. Vous créez un modèle d’appareil en important un modèle de capacité d’appareil et vous ajoutez des vues qui vous permettent d’interagir avec un appareil connecté.
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 1be7087e99ca2e4dc605a8d1c6b9821567aecfef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968137"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Cet article s’applique aux créateurs de solutions et aux développeurs d’appareils.*

Ce tutoriel vous explique comment, en tant que développeur d’appareils, connecter une application cliente Python à votre application Azure IoT Central. L’application Python simule le comportement d’un appareil capteur environnemental. Vous utilisez un exemple de _modèle de capacité d’appareil_ pour créer un _modèle d’appareil_  dans IoT Central. Vous ajoutez des vues au modèle d’appareil pour permettre à un opérateur d’interagir avec un appareil.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Importer un modèle de capacité d’appareil pour créer un modèle d’appareil.
> * Ajouter des vues par défaut et personnalisées à un modèle d’appareil.
> * Publier un modèle d’appareil et ajouter un appareil réel à votre application IoT Central.
> * Créer et exécuter le code d’appareil Python et veiller à ce qu’il se connecte à votre application IoT Central.
> * Afficher la télémétrie simulée envoyée à partir de l’appareil.
> * Utiliser une vue pour gérer les propriétés de l’appareil.
> * Appeler des commandes synchrones et asynchrones pour contrôler l’appareil.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

* Une application Azure IoT Central créée avec le modèle **Application personnalisée**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md). L’application doit avoir été créée à partir du 14/07/2020.
* Une machine de développement où [Python](https://www.python.org/) version 3.7 ou ultérieure est installé. Vous pouvez exécuter `python3 --version` au niveau de la ligne de commande pour vérifier la version. Python est disponible pour un large éventail de systèmes d’exploitation. Les instructions de ce tutoriel supposent que vous exécutez la commande **python3** à l’invite de commandes Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Créer une application Python

Les étapes suivantes vous montrent comment créer une application cliente Python qui se connecte à l’appareil réel que vous avez ajouté à l’application. Cette application Python simule le comportement d’un appareil réel.

1. Dans votre environnement de ligne de commande, accédez au dossier `environmental-sensor` que vous avez créé auparavant.

1. Pour installer les bibliothèques requises, exécutez les commandes suivantes :

    ```cmd
    pip install azure-iot-device
    ```

1. Créez un fichier appelé **environmental_sensor.py** dans le dossier `environmental-sensor`.

1. Ajoutez les instructions `import` suivantes au début du fichier **environmental_sensor.py** :

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Ajoutez la fonction `main` asynchrone et les déclarations de variable suivantes au fichier :

    ```python
    async def main():
        # In a production environment, don't store
        # connection information in the code.
        provisioning_host = 'global.azure-devices-provisioning.net'
        id_scope = '{your Scope ID}'
        registration_id = '{your Device ID}'
        symmetric_key = '{your Primary Key}'
  
        delay = 2
  
        # All the remaining code is nested within this main function

    if __name__ == '__main__':
        asyncio.run(main())
    ```

    Mettez à jour les espaces réservés `{your Scope ID}`, `{your Device ID}` et `{your Primary Key}` avec les valeurs que vous avez notées précédemment. Dans une application réelle, ne codez pas en dur ces informations dans l’application.

    Toutes les définitions de fonction et le code suivants sont imbriqués dans la fonction `main`.

1. Ajoutez les deux fonctions suivantes à l’intérieur de la fonction `main` pour inscrire l’appareil et le connecter à votre application IoT Central. L’inscription utilise le service Azure Device Provisioning :

    ```python
        async def register_device():
            provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
                provisioning_host=provisioning_host,
                registration_id=registration_id,
                id_scope=id_scope,
                symmetric_key=symmetric_key,
            )

            registration_result = await provisioning_device_client.register()

            print(f'Registration result: {registration_result.status}')

            return registration_result
  
        async def connect_device():
            device_client = None
            try:
                registration_result = await register_device()
                if registration_result.status == 'assigned':
                    device_client = IoTHubDeviceClient.create_from_symmetric_key(
                        symmetric_key=symmetric_key,
                        hostname=registration_result.registration_state.assigned_hub,
                        device_id=registration_result.registration_state.device_id
                    )
                    # Connect the client.
                    await device_client.connect()
                    print('Device connected successfully')
            finally:
                return device_client
    ```

1. Ajoutez la fonction suivante à l’intérieur de la fonction `main` pour envoyer la télémétrie à votre application IoT Central :

    ```python
        async def send_telemetry():
            print(f'Sending telemetry from the provisioned device every {delay} seconds')
            while True:
                temp = random.randrange(1, 75)
                humid = random.randrange(30, 99)
                payload = json.dumps(
                    {
                        'temp': temp,
                        'humid': humid
                    })
                msg = Message(payload)
                await device_client.send_message(msg, )
                print(f'Sent message: {msg}')
                await asyncio.sleep(delay)
    ```

    Les noms des éléments de télémétrie (`temp` et `humid`) doivent correspondre aux noms utilisés dans le modèle d’appareil.

1. Ajoutez les fonctions suivantes à l’intérieur de la fonction `main` pour gérer les commandes appelées à partir de votre application IoT Central :

    ```python
        async def blink_command(request):
            print('Received synchronous call to blink')
            response = MethodResponse.create_from_method_request(
                request,
                status = 200,
                payload = {'description': f'Blinking LED every {request.payload} seconds'}
            )
            await device_client.send_method_response(response)  # send response
            print(f'Blinking LED every {request.payload} seconds')

        async def diagnostics_command(request):
            print('Starting asynchronous diagnostics run...')
            response = MethodResponse.create_from_method_request(
                request,
                status = 202
            )
            await device_client.send_method_response(response)  # send response
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Sending property update to confirm command completion')
            await device_client.patch_twin_reported_properties(
                {
                    'rundiagnostics':
                    {
                        'value': f'Diagnostics run complete at {datetime.datetime.today()}.'
                    }
                })

        async def turnon_command(request):
            print('Turning on the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        async def turnoff_command(request):
            print('Turning off the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        commands = {
            'blink': blink_command,
            'rundiagnostics': diagnostics_command,
            'turnon': turnon_command,
            'turnoff': turnoff_command,
        }

        # Define behavior for handling commands
        async def command_listener():
            while True:
                method_request = await device_client.receive_method_request()  # Wait for commands
                await commands[method_request.name](method_request)
    ```

    Les noms des commandes (`blink`, `turnon`, `turnoff` et `rundiagnostics`) doivent correspondre aux noms utilisés dans le modèle d’appareil.

    Actuellement, IoT Central n’utilise pas le schéma de réponse défini dans le modèle de capacité d’appareil. Pour une commande synchrone, la charge utile de réponse peut être n’importe quelle valeur JSON valide. Pour une commande asynchrone, l’appareil doit retourner immédiatement une réponse 202, suivie d’une mise à jour de propriété signalée une fois le travail terminé. Le format de la mise à jour de propriété signalée est le suivant :

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Un opérateur peut afficher la charge utile de réponse dans l’historique des commandes.

1. Ajoutez les fonctions suivantes à l’intérieur de la fonction `main` pour gérer les mises à jour de propriétés envoyées depuis votre application IoT Central. Le message que l’appareil envoie en réponse à la [mise à jour de la propriété accessible en écriture](concepts-telemetry-properties-commands.md#writeable-property-types) doit inclure les champs `av` et `ac`. Le champ `ad` est facultatif :

    ```python
        async def name_setting(value, version):
            await asyncio.sleep(1)
            print(f'Setting name value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'name' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        async def brightness_setting(value, version):
            await asyncio.sleep(5)
            print(f'Setting brightness value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'brightness' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        settings = {
            'name': name_setting,
            'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
            while True:
                patch = await device_client.receive_twin_desired_properties_patch() # blocking
                to_update = patch.keys() & settings.keys()
                await asyncio.gather(
                    *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
                )
    ```

    Quand l’opérateur définit une propriété inscriptible dans l’application IoT Central, l’application utilise une propriété souhaitée du jumeau d’appareil pour envoyer la valeur à l’appareil. L’appareil répond alors en utilisant une propriété signalée du jumeau d’appareil. Quand IoT Central reçoit la valeur de la propriété signalée, il met à jour la vue de propriété avec l’état **synchronisé**.

    Les noms des propriétés (`name` et `brightness`) doivent correspondre aux noms utilisés dans le modèle d’appareil.

1. Ajoutez les fonctions suivantes à l’intérieur de la fonction `main` pour contrôler l’application :

    ```python
        # Define behavior for halting the application
        def stdin_listener():
            while True:
                selection = input('Press Q to quit\n')
                if selection == 'Q' or selection == 'q':
                    print('Quitting...')
                    break
  
        device_client = await connect_device()
  
        if device_client is not None and device_client.connected:
            print('Send reported properties on startup')
            await device_client.patch_twin_reported_properties(
                {
                    'state': 'true',
                    'processorArchitecture': 'ARM',
                    'swVersion': '1.0.0'
                })
            tasks = asyncio.gather(
                send_telemetry(),
                command_listener(),
                twin_patch_listener(),
            )

            # Run the stdin listener in the event loop
            loop = asyncio.get_running_loop()
            user_finished = loop.run_in_executor(None, stdin_listener)

            # Wait for user to indicate they are done listening for method calls
            await user_finished

            # Cancel tasks
            tasks.add_done_callback(lambda r: r.exception())
            tasks.cancel()
            await device_client.disconnect()
  
        else:
            print('Device could not connect')
    ```

1. Enregistrez le fichier **environmental_sensor.py**.

## <a name="run-your-python-application"></a>Exécuter votre application Python

Pour démarrer l’application cliente d’appareil, exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd
python3 environmental_sensor.py
```

Vous pouvez voir que l’appareil se connecte à votre application Azure IoT Central et commence à envoyer des données de télémétrie :

![Exécution de l’application cliente](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Vous pouvez voir comment l’appareil répond aux commandes et aux mises à jour de propriétés :

![Observer l’application cliente](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>Voir les données brutes

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous préférez suivre l’ensemble des tutoriels IoT Central et en savoir plus sur la création d’une solution IoT Central, consultez :

> [!div class="nextstepaction"]
> [Créer un modèle d’appareil de passerelle](./tutorial-define-gateway-device-type.md)

En tant que développeur d’appareils, maintenant que vous avez appris les bases de la création d’un appareil en utilisant Python, les prochaines étapes suggérées sont les suivantes :

* Pour en savoir plus sur le rôle des modèles d’appareils quand vous implémentez votre code d’appareil, consultez [Présentation des modèles d’appareils](./concepts-device-templates.md).
* Lisez [Se connecter à Azure IoT Central](./concepts-get-connected.md) pour en savoir plus sur la façon d’inscrire des appareils auprès d’IoT Central et sur la manière dont IoT Central sécurise les connexions des appareils.