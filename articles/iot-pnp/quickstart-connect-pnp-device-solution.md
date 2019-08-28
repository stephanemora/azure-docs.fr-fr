---
title: Interagir avec un appareil IoT Plug-and-Play (préversion) connecté à une solution Azure IoT | Microsoft Docs
description: Utilisez Node.js pour vous connecter à un appareil IoT Plug-and-Play en préversion connecté à votre solution Azure IoT et pour interagir avec lui.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 246d3eac8f9d8aff6d603ea8686e430ce0f772ea
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881588"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play en préversion connecté à votre solution

La préversion d’IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec les fonctionnalités d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce démarrage rapide vous montre comment utiliser Node.js pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

## <a name="prerequisites"></a>Prérequis

Téléchargez et installez Node.js à partir de [nodejs.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Vous avez également besoin d’un hub Azure IoT dans votre abonnement Azure pour suivre ce démarrage rapide. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ajoutez l’extension Microsoft Azure IoT pour Azure CLI :

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Exécutez la commande suivante pour créer une identité d’appareil dans votre hub IoT. Remplacez les espaces réservés **YourIoTHubName** et **YourDevice** par vos noms réels. Si vous n’avez pas de IoT Hub, [suivez ces instructions pour en créer un](../iot-hub/iot-hub-create-using-cli.md) :

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion_ de l’appareil que vous venez d’inscrire :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub :

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Connexion de votre appareil

Dans ce démarrage rapide, vous utilisez un exemple de capteur environnemental écrit en Node.js en tant qu’appareil IoT Plug-and-Play. Les instructions suivantes vous montrent comment installer et exécuter l’appareil :

1. Cloner le référentiel GitHub :

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Dans le terminal, accédez au dossier racine de votre référentiel cloné, accédez au dossier **/azure-iot-samples-node/digital-twins/Quickstarts/Device**, puis exécutez la commande suivante pour installer toutes les dépendances :

    ```cmd/sh
    npm install
    ```

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Pour exécutez l’exemple, utilisez la commande suivante :

    ```cmd/sh
    node sample_device.js
    ```

1. Vous voyez des messages indiquant que l’appareil a envoyé des données de télémétrie et ses propriétés. L’appareil est maintenant prêt à recevoir des commandes et des mises à jour de propriétés. Ne fermez pas ce terminal. Vous en aurez besoin plus tard pour confirmer que les exemples de service fonctionnaient également.

## <a name="build-the-solution"></a>Générez la solution.

Dans ce démarrage rapide, vous utilisez un exemple de solution IoT dans Node.js pour interagir avec l’exemple d’appareil.

1. Ouvrez un autre terminal. Accédez au dossier de votre référentiel cloné, puis accédez au dossier **/azure-iot-samples-node/digital-twins/Quickstarts/Service**. Installez toutes les dépendances en exécutant la commande suivante :

    ```cmd/sh
    npm install
    ```

1. Configurer la _chaîne de connexion du hub_ :

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Lire une propriété

1. Lorsque vous connectez l’appareil dans le terminal, le message suivant s’affiche :

    ```cmd/sh
    reported state property as online
    ```

1. Ouvrez le fichier **get_digital_twin.js**. Remplacez `deviceID` par votre ID d’appareil et enregistrez le fichier.

1. Accédez au terminal que vous avez ouvert pour exécuter l’exemple de service, puis exécutez la commande suivante :

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Dans la sortie, sous le composant _environmentalSensor_, vous voyez que le même état a été signalé :

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Mettre à jour une propriété accessible en écriture

1. Ouvrez le fichier **update_digital_twin_property.js**.

1. Au début du fichier, il existe un ensemble de constantes définies avec des espaces réservés en majuscules. Remplacez **deviceID** par votre ID d’appareil réel, mettez à jour les constantes avec les valeurs suivantes, puis enregistrez le fichier :

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Accédez au terminal que vous avez ouvert pour exécuter l’exemple de service, puis utilisez la commande suivante pour exécuter l’exemple :

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. Dans le terminal, vous voyez les informations de jumeau numérique associées à votre appareil. Recherchez le composant _environmentalSensor_. Vous voyez la nouvelle valeur de luminosité 60.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. Revenez à votre terminal de _service_, réexécutez la commande ci-dessous pour confirmer la mise à jour de la propriété.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. Dans la sortie, sous le composant environmentalSensor, vous voyez que la valeur de luminosité mise à jour a été signalée. Remarque : l’appareil peut prendre un certain temps pour terminer la mise à jour. Vous pouvez répéter cette étape jusqu’à ce que l’appareil ait réellement traité la mise à jour de propriété.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Appeler une commande

1. Ouvrez le fichier **invoke_command.js**.

1. Au début du fichier, remplacez `deviceID` par votre ID d’appareil réel. Mettez à jour les constantes avec les valeurs suivantes, puis enregistrez le fichier :

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Accédez au terminal que vous avez ouvert pour exécuter l’exemple de service. Utilisez la commande suivante pour exécuter cet exemple :

    ```cmd/sh
    node invoke_command.js
    ```

1. Dans le terminal, une opération réussie ressemble à la sortie suivante :

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Accédez au terminal de l’_appareil_. Vous voyez que la commande a été reconnue :

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer avec des articles ultérieurs, vous pouvez conserver les ressources que vous avez utilisées dans ce démarrage rapide. Dans le cas contraire, vous pouvez supprimer les ressources que vous avez créées pour ce démarrage rapide afin d’éviter des frais supplémentaires.

Pour supprimer le hub et l’appareil inscrit, effectuez les étapes suivantes à l’aide d’Azure CLI :

```azurecli-interactive
az group delete --name <Your group name>
```

Pour supprimer uniquement l’appareil que vous avez inscrit avec votre IoT Hub, procédez comme suit à l’aide d’Azure CLI :

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil et interagir avec celui-ci](howto-develop-solution.md)
