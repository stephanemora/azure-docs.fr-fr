---
title: Démarrage rapide pour envoyer la télémétrie des appareils à Azure IoT Central (Python)
description: Dans ce démarrage rapide, vous utilisez le Kit de développement logiciel (SDK) Azure IoT Hub Device pour Python afin d’envoyer les données de télémétrie d’un appareil à IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 04/27/2021
ms.openlocfilehash: 2464d9d4e6a945620bdbf80728aa3e7df0e2776e
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226705"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Démarrage rapide : Envoyer des données de télémétrie d’un appareil à Azure IoT Central (Python)

**S’applique à** : [Développeurs d’applications pour appareils](about-iot-develop.md#device-application-development)<br>
**Durée d’exécution** : 12 minutes

Dans ce guide de démarrage rapide, vous allez apprendre un workflow simple de développement d’applications IoT. Tout d’abord, vous créez une application cloud pour gérer des appareils dans Azure IoT Central. Vous utilisez ensuite le kit SDK Azure IoT Python pour créer un appareil à thermostat simulé, le connecter à IoT Central et envoyer la télémétrie.

## <a name="prerequisites"></a>Prérequis
- [Python 3.7](https://www.python.org/downloads/) ou version ultérieure. Pour vérifier votre version de Python, exécutez `python --version`. 

[!INCLUDE [iot-develop-create-central-app-with-device](../../includes/iot-develop-create-central-app-with-device.md)]

## <a name="configure-a-simulated-device"></a>Configurer un appareil simulé
Dans cette section, vous utilisez les exemples du SDK Python pour configurer un appareil à thermostat simulé.

1. Ouvrez un terminal à l’aide de Windows CMD, PowerShell ou Bash (pour Windows ou Linux). Vous allez utiliser le terminal pour installer le Kit de développement logiciel (SDK) Python, mettre à jour les variables d’environnement et exécuter l’exemple de code Python.

1. Copiez les [exemples d’appareils du Kit de développement logiciel (SDK) Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) sur votre ordinateur local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Accédez au répertoire des exemples.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Installez le Kit de développement logiciel (SDK) Azure IoT Python.
    ```console
    pip3 install azure-iot-device
    ```

1. Définissez chacune des variables d’environnement suivantes pour permettre à votre appareil simulé de se connecter à IoT Central. Pour `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` et `IOTHUB_DEVICE_DPS_DEVICE_ID`, utilisez les valeurs de connexion de l’appareil que vous avez enregistrées.

    **Windows CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de variable.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

## <a name="send-telemetry"></a>Envoyer des données de télémétrie
Après avoir configuré votre système, vous êtes prêt à exécuter le code. Le code crée un thermostat simulé, se connecte à votre application IoT Central et à l’instance de l’appareil et envoie de la télémétrie.

1. Dans votre terminal, exécutez l’exemple de code suivant. Si vous le souhaitez, vous pouvez exécuter l’exemple de code Python dans votre environnement de développement intégré Python.
    ```console
    python temp_controller_with_thermostats.py
    ```

    Une fois que votre appareil simulé se connecte à votre application IoT Central, il se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer de la télémétrie. Les détails de la connexion et la sortie de la télémétrie sont affichés dans votre console : 
    
    ```output
    c:\azure-iot-sdk-python\azure-iot-device\samples\pnp>python temp_controller_with_thermostats.py
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
    Updating pnp properties for root interface
    {'serialNumber': 'alohomora'}
    Updating pnp properties for thermostat1
    {'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
    Updating pnp properties for thermostat2
    {'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
    Updating pnp properties for deviceInformation
    {'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry from various components
    Sent message
    {"temperature": 33}
    ```

1. Dans IoT Central, sélectionnez **Appareils**, cliquez sur le nom de votre appareil, puis sélectionnez l’onglet **Données brutes**. Cette vue affiche la télémétrie brute reçue de l’appareil simulé. 

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Sortie brute de la télémétrie de l’appareil IoT Central":::
    
    Votre appareil est maintenant connecté en toute sécurité et envoie des données de télémétrie à Azure IoT.
    
## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez plus besoin des ressources IoT Central créées dans ce guide de démarrage rapide, vous pouvez les supprimer. En option, si vous envisagez de continuer à suivre la documentation de ce guide, vous pouvez conserver l’application que vous avez créée et la réutiliser dans d’autres exemples.

Pour supprimer l’exemple d’application Azure IoT Central et tous ses appareils et ressources :
1. Sélectionnez **Administration** > **Votre application**.
1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris un workflow de base d’application Azure IoT pour connecter de façon sécurisée un appareil au cloud et envoyer des données de télémétrie appareil-à-cloud. Vous avez utilisé Azure IoT Central pour créer une application et un appareil, puis avez utilisé le Kit de développement logiciel (SDK) Azure IoT Python pour créer un appareil simulé et envoyer la télémétrie. Vous avez également utilisé IoT Central pour surveiller les données de télémétrie.

En guise d’étape suivante, explorez IoT Central en tant que solution pour héberger vos appareils et explorez les exemples de code Python du SDK Azure.

> [!div class="nextstepaction"]
> [Créer une application IoT Central](../iot-central/core/quick-deploy-iot-central.md)
> [!div class="nextstepaction"]
> [Exemples d’appareils asynchrones](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)
> [!div class="nextstepaction"]
> [Exemples d’appareils synchrones](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)
