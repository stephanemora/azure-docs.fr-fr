---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 984849cddceb2476bd9d0870da47a33770c4a36a
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776406"
---
## <a name="prerequisites"></a>Prérequis
- [Python](https://www.python.org/downloads/) version 3.7 ou ultérieure. Pour vérifier votre version de Python, exécutez `python --version`.
- [Git](https://git-scm.com/downloads).
- Vous pouvez exécuter ce guide de démarrage rapide sur Linux ou Windows. Les commandes de l’interpréteur de commandes utilisent le séparateur de chemin Linux standard `/`. Si vous utilisez Windows, remplacez ces séparateurs par le séparateur de chemin d’accès Windows `\`.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>Exécuter un appareil simulé
Dans cette section, vous allez configurer votre environnement local, installer le kit SDK Python d’appareil Azure IoT et exécuter un exemple qui crée un contrôleur de température simulé.

### <a name="configure-your-environment"></a>Configurer votre environnement

1. Ouvrez une console à l’aide de Windows CMD, PowerShell ou Bash.

1. Définissez les variables d’environnement suivantes à l’aide des commandes appropriées pour votre console. L’appareil simulé utilise ces valeurs pour se connecter à IoT Central. Pour `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` et `IOTHUB_DEVICE_DPS_DEVICE_ID`, utilisez les valeurs de connexion d’appareil que vous avez enregistrées précédemment.

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

### <a name="install-the-sdk-and-samples"></a>Installer le kit SDK et les exemples

1. Copiez le kit SDK Python d’appareil Azure IoT sur votre ordinateur local.

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

### <a name="run-the-code"></a>Exécuter le code

1. Dans votre console, exécutez l’exemple de code suivant à partir du kit SDK. L’exemple crée un contrôleur de température simulé avec des capteurs de thermostat.
    ```console
    python temp_controller_with_thermostats.py
    ```

    Une fois que votre appareil simulé se connecte à votre application IoT Central, il se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer de la télémétrie. Les détails de la connexion et la sortie de la télémétrie sont affichés dans votre console : 
    
    ```output
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