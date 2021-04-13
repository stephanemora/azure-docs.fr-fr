---
title: Démarrage rapide pour envoyer la télémétrie des appareils à Azure IoT Central (Python)
description: Dans ce démarrage rapide, vous utilisez le Kit de développement logiciel (SDK) Azure IoT Hub Device pour Python afin d’envoyer les données de télémétrie d’un appareil à IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047165"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Démarrage rapide : Envoyer des données de télémétrie d’un appareil à Azure IoT Central (Python)

**S’applique à** : [Développement d’applications pour appareils](about-iot-develop.md#device-application-development)

Dans ce démarrage rapide, vous allez apprendre un workflow de base relatif au développement d’applications pour appareils IoT. Vous utilisez d’abord Azure IoT Central pour créer une application cloud. Vous utilisez ensuite le Kit de développement logiciel (SDK) Azure IoT Python pour créer un appareil simulé, vous connecter à IoT Central et envoyer des données de télémétrie appareil-à-cloud. 

## <a name="prerequisites"></a>Prérequis
- [Python 3.7+](https://www.python.org/downloads/). Pour les autres versions de Python prises en charge, consultez les [fonctionnalités des appareils Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Pour vous assurer que votre version de Python est à jour, exécutez `python --version`. Si vous avez installé Python 2 et Python 3 et que vous utilisez un environnement Python 3, installez toutes les bibliothèques en utilisant `pip3`. L’exécution de `pip3` permet de s’assurer que les bibliothèques sont installées sur votre runtime Python 3.
    > [!IMPORTANT]
    > Dans le programme d’installation de Python, sélectionnez l’option permettant d’**ajouter Python à PATH**. Si vous avez déjà installé Python 3.7 ou une version ultérieure, vérifiez que vous avez ajouté le dossier d’installation de Python à la variable d’environnement `PATH`.

## <a name="create-an-application"></a>Créer une application
Dans cette section, vous créez une application IoT Central. IoT Central est une plateforme d’applications IoT basée sur un portail qui permet de réduire la complexité et le coût du développement et de la gestion des solutions IoT.

Pour créer une application Azure IoT Central :
1. Accédez à [Azure IoT Central](https://apps.azureiotcentral.com/) et connectez-vous avec un compte Microsoft personnel, professionnel ou scolaire.
1. Accédez à **Générer**, puis sélectionnez **Applications personnalisées**.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text=" Page d’accueil d’IoT Central ":::
1. Dans **Nom de l’application**, entrez un nom unique ou utilisez le nom généré.
1. Dans **URL**, entrez un préfixe d’URL d’application facile à mémoriser ou utilisez le préfixe d’URL généré.
1. Laissez **Modèle d’application** défini sur *Application personnalisée*. La liste déroulante peut afficher d’autres options si des modèles existent déjà dans votre compte.
1. Sélectionnez une option de **plan tarifaire**. 
    - Pour utiliser l’application gratuitement pendant sept jours, sélectionnez **Gratuit**. Vous pouvez convertir une application gratuite en tarification standard avant qu’elle n’expire.
    - Si vous le souhaitez, vous pouvez sélectionner un plan tarifaire standard. Si vous sélectionnez la tarification standard, d’autres options s’affichent et vous devez définir un **répertoire**, un **abonnement Azure** et un **emplacement**. Pour en savoir plus sur la tarification, consultez [Tarification d’Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Annuaire** correspond à l’annuaire Azure Active Directory dans lequel vous créez votre application. Un annuaire Azure Active Directory contient les identités des utilisateurs, les informations d’identification et d’autres informations de l’organisation. Si vous n’avez pas d’instance d’Azure Active Directory, il s’en crée une automatiquement quand vous créez un abonnement Azure.
        - Un **abonnement Azure** vous permet de créer des instances de services Azure. IoT Central provisionne des ressources dans votre abonnement. Si vous n’avez pas d’abonnement Azure, vous pouvez [en créer un gratuitement](https://azure.microsoft.com/free/). Après avoir créé l’abonnement, revenez à la page **Nouvelle application** d’IoT Central. Votre nouvel abonnement apparaît dans la liste déroulante **Abonnement Azure**.
        - L’**emplacement** désigne la [région géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) dans laquelle vous créez une application. Sélectionnez la région qui est physiquement la plus proche de vos appareils, de façon à obtenir des performances optimales. Une fois que vous aurez choisi un emplacement, vous ne pourrez plus déplacer l’application vers un autre emplacement.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Boîte de dialogue Nouvelle application d’IoT Central":::
1. Sélectionnez **Create** (Créer).
    
    Après avoir créé l’application, IoT Central vous redirige vers le tableau de bord de l’application.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="Tableau de bord Nouvelle application d’IoT Central":::

## <a name="add-a-device"></a>Ajout d’un appareil
Dans cette section, vous ajoutez un nouvel appareil à votre application IoT Central. L’appareil est l’instance d’un modèle d’appareil qui représente un appareil réel ou simulé auquel vous allez connecter à l’application. 

Pour créer un nouvel appareil :
1. Dans le volet gauche, sélectionnez **Appareils**, puis sélectionnez **+Nouveau**. Cela ouvre la boîte de dialogue du nouvel appareil.
1. Laissez **Modèle d’appareil** défini sur *Non attribué*.

    > [!NOTE]
    > Dans ce démarrage rapide, pour des raisons de simplicité, vous connectez un appareil simulé qui utilise un modèle non attribué. Si vous continuez à utiliser IoT Central pour gérer les appareils, vous apprendrez à utiliser des modèles d’appareil. Pour obtenir une vue d’ensemble de l’utilisation des modèles d’appareil, consultez [Démarrage rapide : Ajouter un appareil simulé à votre application IoT Central](../iot-central/core/quick-create-simulated-device.md).
1. Définissez un **nom d’appareil** convivial et un **ID d’appareil**. Si vous le souhaitez, utilisez les valeurs générées.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Boîte de dialogue Nouvel appareil d’IoT Central":::
1. Sélectionnez **Create** (Créer).

    L’appareil créé s’affiche dans la liste **Tous les appareils**.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Liste Tous les appareils d’IoT Central":::
    
Pour récupérer les détails de connexion pour le nouvel appareil :
1. Dans la liste **Tous les appareils**, double-cliquez sur le nom de l’appareil lié pour afficher les détails. 
1. Dans le menu supérieur, sélectionnez **Se connecter**.

    La boîte de dialogue **Connexion de l’appareil** affiche les détails de la connexion : :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="Détails de connexion de l’appareil d’IoT Central":::
1. Copiez les valeurs suivantes de la boîte de dialogue **Connexion de l’appareil** dans un emplacement sûr. Vous les utiliserez dans la section suivante pour connecter votre appareil à IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Envoyer des messages et surveiller la télémétrie
Dans cette section, vous allez utiliser le Kit de développement logiciel (SDK) Python pour créer un appareil simulé et envoyer des données de télémétrie à votre application IoT Central. 

1. Ouvrez un terminal à l’aide de Windows CMD, PowerShell ou Bash (pour Windows ou Linux). Vous allez utiliser le terminal pour installer le Kit de développement logiciel (SDK) Python, mettre à jour les variables d’environnement et exécuter l’exemple de code Python.

1. Copiez les [exemples d’appareils du Kit de développement logiciel (SDK) Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) sur votre ordinateur local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Accédez au répertoire *azure-iot-sdk-python/azure-iot-device/samples*.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Installez le Kit de développement logiciel (SDK) Azure IoT Python.

    ```console
    pip install azure-iot-device
    ```

1. Définissez chacune des variables d’environnement suivantes pour permettre à votre appareil simulé de se connecter à IoT Central. Pour `ID_SCOPE`, `DEVICE_ID` et `DEVICE_KEY`, utilisez les valeurs que vous avez enregistrées depuis la boîte de dialogue *Connexion de l’appareil* d’IoT Central.

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après la chaîne de connexion ou d’autres valeurs de variable.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. Dans votre terminal, exécutez le code de l’exemple de fichier *simple_send_temperature.py. Ce code accède à l’appareil IoT simulé et envoie un message à IoT Central.

    Pour exécuter l’exemple Python à partir du terminal :
    ```console
    python ./simple_send_temperature.py
    ```

    Si vous le souhaitez, vous pouvez exécuter le code Python à partir de l’exemple dans votre environnement de développement intégré Python :
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Lorsque le code Python envoie un message de votre appareil à votre application IoT Central, les messages s’affichent sous l’onglet **Données brutes** de votre appareil dans IoT Central. Vous devrez peut-être actualiser la page pour afficher les messages récents.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Capture d’écran de la sortie des données brutes d’IoT Central":::

Votre appareil est maintenant connecté en toute sécurité et envoie des données de télémétrie à Azure IoT.

## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez plus besoin des ressources IoT Central créées dans ce tutoriel, vous pouvez les supprimer du portail IoT Central. En option, si vous envisagez de continuer à suivre la documentation de ce guide, vous pouvez conserver l’application que vous avez créée et la réutiliser dans d’autres exemples.

Pour supprimer l’exemple d’application Azure IoT Central et tous ses appareils et ressources :
1. Sélectionnez **Administration** > **Votre application**.
1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris un workflow de base d’application Azure IoT pour connecter de façon sécurisée un appareil au cloud et envoyer des données de télémétrie appareil-à-cloud. Vous avez utilisé Azure IoT Central pour créer une application et un appareil, puis avez utilisé le Kit de développement logiciel (SDK) Azure IoT Python pour créer un appareil simulé et envoyer la télémétrie. Vous avez également utilisé IoT Central pour surveiller les données de télémétrie.

L’étape suivante consiste à explorer le Kit de développement logiciel (SDK) Azure IoT Python à l’aide d’exemples d’applications.

- [Exemples asynchrones](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios) : Ce répertoire contient des exemples Python asynchrones pour d’autres scénarios IoT Hub.
- [Exemples synchrones](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples) : Ce répertoire contient des exemples Python à utiliser avec Python 2.7 ou des scénarios de compatibilité synchrone pour Python 3.6 et versions ultérieures
- [Exemples IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios) : Ce répertoire contient des exemples Python pour l’utilisation des modules Edge et des appareils en aval.
