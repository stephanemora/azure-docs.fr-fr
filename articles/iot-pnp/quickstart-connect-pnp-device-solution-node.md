---
title: Interagir avec un appareil IoT Plug-and-Play (préversion) connecté à une solution Azure IoT | Microsoft Docs
description: Utilisez Node.js pour vous connecter à un appareil IoT Plug-and-Play en préversion connecté à votre solution Azure IoT et pour interagir avec lui.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550738"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play en préversion connecté à votre solution (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

La préversion d’IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec les fonctionnalités d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce démarrage rapide vous montre comment utiliser Node.js pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide de démarrage rapide, vous avez besoin de Node.js sur votre ordinateur de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Dans ce démarrage rapide, vous utilisez un exemple de capteur environnemental écrit en Node.js en tant qu’appareil IoT Plug-and-Play. Les instructions suivantes vous montrent comment installer et exécuter l’appareil :

1. Ouvrez une fenêtre de terminal dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT Samples for Node.js](https://github.com/azure-samples/azure-iot-samples-node) à cet emplacement :

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Cette fenêtre de terminal est à présent utilisée comme terminal de votre _appareil_. Accédez au dossier de votre dépôt cloné, puis accédez au dossier **/azure-iot-samples-node/digital-twins/Quickstarts/Device**. Installez toutes les dépendances en exécutant la commande suivante :

    ```cmd/sh
    npm install
    ```

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Pour exécutez l’exemple, utilisez la commande suivante :

    ```cmd/sh
    node sample_device.js
    ```

1. Des messages s’affichent pour indiquer que l’appareil a envoyé des informations et qu’il s’est signalé comme étant en ligne. L’appareil a donc commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés. Ne fermez pas ce terminal. Vous en aurez besoin plus tard pour confirmer que les exemples de service ont bien fonctionné.

## <a name="run-the-sample-solution"></a>Exécuter l’exemple de solution

Dans ce démarrage rapide, vous utilisez un exemple de solution IoT dans Node.js pour interagir avec l’exemple d’appareil.

1. Ouvrez une autre fenêtre de terminal (votre terminal de _service_). Accédez au dossier de votre référentiel cloné, puis accédez au dossier **/azure-iot-samples-node/digital-twins/Quickstarts/Service**. Installez toutes les dépendances en exécutant la commande suivante :

    ```cmd/sh
    npm install
    ```

1. Configurez la _chaîne de connexion au hub IoT_ pour permettre au service de s’y connecter :

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Lire une propriété

1. Quand vous avez connecté l’_appareil_ dans son terminal, vous avez vu le message suivant indiquant son état en ligne :

    ```cmd/sh
    reported state property as online
    ```

1. Dans le dossier **/azure-iot-samples-node/digital-twins/Quickstarts/Service**, ouvrez le fichier **get_digital_twin.js**. Remplacez l’espace réservé `<DEVICE_ID_GOES_HERE>` par votre ID d’appareil et enregistrez le fichier.

1. Accédez au terminal de _service_ et utilisez la commande suivante pour exécuter l’exemple de lecture des informations de l’appareil :

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Faites défiler la sortie du terminal de _service_ jusqu’au composant `environmentalSensor`. Vous pouvez constater que la propriété `state` est signalée comme étant en ligne (_online_) :

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Mettre à jour une propriété accessible en écriture

1. Ouvrez le fichier **update_digital_twin_property.js**.

1. Au début du fichier, il existe un ensemble de constantes définies avec des espaces réservés en majuscules. Remplacez l’espace réservé `<DEVICE_ID_GOES_HERE>` par votre ID d’appareil réel, mettez à jour les constantes restantes avec les valeurs suivantes, puis enregistrez le fichier :

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Accédez au terminal de _service_ et utilisez la commande suivante pour exécuter l’exemple de mise à jour de la propriété :

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. La sortie du terminal de _service_ présente les informations à jour sur l’appareil. Faites défiler jusqu’au composant `environmentalSensor` pour voir la nouvelle valeur de luminosité (42).

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Accédez à votre terminal d’_appareil_. Vous voyez que l’appareil a reçu la mise à jour :

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Revenez à votre terminal de _service_, puis réexécutez la commande ci-dessous pour obtenir les informations sur l’appareil et confirmer que la propriété a été mise à jour.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. Dans la sortie du terminal de _service_, sous le composant `environmentalSensor`, vous voyez que la valeur de luminosité mise à jour a été signalée. Remarque : l’appareil peut prendre un certain temps pour terminer la mise à jour. Vous pouvez répéter cette étape jusqu’à ce que l’appareil ait réellement traité la mise à jour de propriété.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Appeler une commande

1. Ouvrez le fichier **invoke_command.js**.

1. Au début du fichier, remplacez l’espace réservé `<DEVICE_ID_GOES_HERE>` par votre ID d’appareil réel. Mettez à jour les constantes restantes avec les valeurs suivantes, puis enregistrez le fichier :

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Accédez au terminal de _service_. Utilisez la commande suivante pour exécuter exemple d’appel de la commande :

    ```cmd/sh
    node invoke_command.js
    ```

1. La sortie du terminal de _service_ doit indiquer la confirmation suivante :

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Accédez au terminal de l’_appareil_. Vous voyez que la commande a été reconnue :

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil et interagir avec celui-ci](howto-develop-solution.md)
