---
title: Interagir avec un appareil IoT Plug-and-Play (préversion) connecté à une solution Azure IoT (Node.js) | Microsoft Docs
description: Utilisez Node.js pour vous connecter à un appareil IoT Plug-and-Play en préversion connecté à votre solution Azure IoT et pour interagir avec lui.
author: elhorton
ms.author: elhorton
ms.date: 07/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9ddca58e166baa1e94fcc3edcfbbc64abd578049
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352599"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play en préversion connecté à votre solution (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

La préversion d’IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec les fonctionnalités d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce démarrage rapide vous montre comment utiliser Node.js pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous avez besoin de Node.js sur votre ordinateur de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
node --version
```

Installez le [Node service SDK avec la prise en charge d’IoT Plug-and-Play](https://www.npmjs.com/package/azure-iot-digitaltwins-service) en exécutant la commande suivante :

```cmd/sh
npm i azure-iot-digitaltwins-service
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce démarrage rapide :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Exécutez la commande suivante pour obtenir la _chaîne de connexion_ à l’appareil que vous avez ajouté au hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce démarrage rapide :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Dans ce démarrage rapide, vous pouvez utiliser un exemple de thermostat écrit en Node.js en tant qu’appareil IoT Plug-and-Play. Pour exécuter l’exemple d’appareil :

1. Ouvrez une fenêtre de terminal dans un dossier de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [SDK Microsoft Azure IoT pour for Node.js](https://github.com/Azure/azure-iot-sdk-node) à cet emplacement :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Cette fenêtre de terminal est utilisée en tant que terminal **device**. Accédez au dossier de votre dépôt cloné, puis accédez au dossier */azure-iot-sdk-node/device/samples/pnp*. Installez toutes les dépendances en exécutant la commande suivante :

    ```cmd/sh
    npm install
    ```

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Pour exécutez l’exemple d’appareil à thermostat, utilisez la commande suivante :

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Des messages s’affichent pour indiquer que l’appareil a envoyé des informations et qu’il s’est signalé comme étant en ligne. Ces messages indiquent que l’appareil a commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés. Ne fermez pas ce terminal. Vous en aurez besoin pour confirmer que l’exemple de service fonctionne également.

## <a name="run-the-sample-solution"></a>Exécuter l’exemple de solution

Dans ce guide de démarrage rapide, vous utilisez un exemple de solution IoT en Node.js pour interagir avec l’exemple d’appareil que vous venez de configurer.

1. Ouvrez une autre fenêtre de terminal à utiliser comme terminal **service**. Le SDK du service étant en préversion, vous devez cloner les exemples à partir d’une [branche de préversion du SDK Node](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh) :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b public-preview-pnp
    ```

1. Accédez au dossier de cett branche de dépôt cloné, puis accédez au dossier */azure-iot-samples-node/digital-twins/samples/service/javascript*. Installez toutes les dépendances en exécutant la commande suivante :

    ```cmd/sh
    npm install
    ```

1. Configurez les variables d’environnement pour votre ID d’appareil et la _chaîne de connexion IoT Hub_ :

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Lire une propriété

1. Lorsque vous avez exécuté l’exemple de thermostat dans le terminal de l’**​​appareil**, vous avez vu les messages suivants indiquant son état en ligne :

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Accédez au terminal de **service** et utilisez la commande suivante pour exécuter l’exemple de lecture des informations de l’appareil :

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Dans la sortie de terminal du **service**, notez la réponse du jumeau numérique. Vous voyez l’ID de modèle d’appareil et les propriétés associées signalées :

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. L’extrait de code suivant montre le code dans *get_digital_twin.js* qui extrait l’ID de modèle du jumeau d’appareil :

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

Dans ce scénario, il génère la sortie suivante : `Model Id: dtmi:com:example:Thermostat;1`.

### <a name="update-a-writable-property"></a>Mettre à jour une propriété accessible en écriture

1. Ouvrez le fichier *update_digital_twin_property.js* dans un éditeur de code.

1. Examinez l’exemple de code. Vous pouvez voir comment créer un correctif JSON pour mettre à jour le jumeau numérique de votre appareil. Dans cet exemple, le code remplace la température du thermostat par la valeur 42 :

    ```javascript
    const patch = [{
        op: 'add',
        path: 'targetTemperature',
        value: '42'
      }]
    ```

1. Dans le terminal du **service**, utilisez la commande suivante pour exécuter l’exemple de mise à jour de la propriété :

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. La sortie du terminal de **service** présente les informations à jour sur l’appareil. Faites défiler jusqu’au composant `thermostat1` pour voir la nouvelle valeur `targetTemperature` (42) :

    ```json
    "modelId": "dtmi:com:example:Thermostat;1",
        "version": 12,
        "properties": {
            "desired": {
                "targetTemperature": "42",
                "$metadata": {
                    "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                    "$lastUpdatedVersion": 5,
                    "targetTemperature": {
                        "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                        "$lastUpdatedVersion": 5
                    }
                },
                "$version": 5
            },
            "reported": {
                "serialNumber": "123abc",
                "maxTempSinceLastReboot": 32.279942997143785,
                "targetTemperature": {
                    "value": "42",
                    "ac": 200,
                    "ad": "Successfully executed patch for targetTemperature",
                    "av": 2
                },
    ```

1. Dans votre terminal d’**appareil**, vous voyez que l’appareil a reçu la mise à jour :

    ```cmd/sh
    Received an update for targetTemperature: 42
    updated the property
    ```

1. Dans votre terminal de **service**, exécutez la commande suivante pour confirmer la mise à jour de la propriété :

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Dans la sortie de terminal du **service**, dans la réponse de jumeau numérique du composant `thermostat1`, vous voyez la température de la cible mise à jour signalée. L’appareil peut prendre un certain temps pour terminer la mise à jour. Répétez cette étape jusqu’à ce que l’appareil ait traité la mise à jour de propriété.

    ```json
    "$model": "dtmi:com:example:Thermostat;1",
    "targetTemperature": {
      "desiredValue": 42,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch for targetTemperature",
      "lastUpdateTime": "2020-07-09T13:55:30.5062641Z"
    }
    ```

### <a name="invoke-a-command"></a>Appeler une commande

1. Ouvrez le fichier *invoke_command.js* et passez en revue le code.

1. Accédez au terminal de **service**. Utilisez la commande suivante pour exécuter exemple d’appel de la commande :

    ```cmd/sh
    node invoke_command.js
    ```

1. La sortie du terminal de **service** montre la confirmation suivante :

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. Dans le terminal de l’**appareil**, vous voyez que la commande a été reconnue :

    ```cmd/sh
    MaxMinReport [object Object]
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour découvrir plus d’informations sur les modèles d’appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide du développeur pour la modélisation avec la préversion d’IoT Plug-and-Play](concepts-developer-guide.md)
