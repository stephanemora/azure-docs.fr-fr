---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6499ca664e56ef6484a5d83d78fee5e3b6c0e800
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776413"
---
## <a name="prerequisites"></a>Prérequis
- [Node.js](https://nodejs.org/) version 6 ou ultérieure. Pour vérifier votre version, exécutez `node --version` dans votre application console.
- [Git](https://git-scm.com/downloads).
- Vous pouvez exécuter ce guide de démarrage rapide sur Linux ou Windows. Les commandes de l’interpréteur de commandes utilisent le séparateur de chemin Linux standard `/`. Si vous utilisez Windows, remplacez ces séparateurs par le séparateur de chemin d’accès Windows `\`.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>Exécuter un appareil simulé
Dans cette section, vous allez configurer votre environnement local, installer le kit SDK Node.js d’appareil Azure IoT et exécuter un exemple qui crée un contrôleur de température simulé.

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

1. Copiez le kit SDK Node.js d’appareil Azure IoT sur votre ordinateur local.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Accédez au répertoire des exemples.
    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```
1. Installez le Kit de développement logiciel (SDK) Azure IoT Node.js et les dépendances nécessaires :
    ```console
    npm install
    ```

### <a name="run-the-code"></a>Exécuter le code

1. Dans votre console, exécutez l’exemple de code suivant à partir du kit SDK. L’exemple crée un contrôleur de température simulé avec des capteurs de thermostat.
    ```console
    node pnpTemperatureController.js
    ```

    Une fois que votre appareil simulé se connecte à votre application IoT Central, il se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer de la télémétrie. Les détails de la connexion et la sortie de la télémétrie sont affichés dans votre console : 
    
    ```output
    registration succeeded
    assigned hub=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net
    deviceId=my-sdk-device
    payload=undefined
    Connecting using connection string: HostName=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net;DeviceId=my-sdk-device;SharedAccessKey=xxxxxxxxxxxxxxxxxxxxxxxxxxx
    Enabling the commands on the client
    Please enter q or Q to exit sample.
    The following properties will be updated for root interface.
    { serialNumber: 'alwinexlepaho8329' }
    The following properties will be updated for component: thermostat1
    {
      thermostat1: { maxTempSinceLastReboot: 40.53506261527863, __t: 'c' }
    }
    The following properties will be updated for component: thermostat2
    {
      thermostat2: { maxTempSinceLastReboot: 89.55136974144273, __t: 'c' }
    }
    The following properties will be updated for component: deviceInformation
    {
      deviceInformation: {
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
        __t: 'c'
      }
    }
    executed sample
    Received an update for device with value: {"$version":1}
    Properties have been reported for root interface.
    Properties have been reported for component: thermostat1
    Properties have been reported for component: thermostat2
    Properties have been reported for component: deviceInformation
    Sending telemetry message 0 from component: thermostat1
    Sending telemetry message 0 from component: thermostat2
    ```