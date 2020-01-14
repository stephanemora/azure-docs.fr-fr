---
title: Interagir avec un appareil IoT Plug-and-Play (préversion) connecté à une solution Azure IoT | Microsoft Docs
description: Utilisez C# (.NET) pour vous connecter à un appareil IoT Plug-and-Play en préversion connecté à votre solution Azure IoT et pour interagir avec lui.
author: baanders
ms.author: baanders
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9e8bc6c4ad7ed852ddaae2e193b91887fcd92e47
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550772"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play en préversion connecté à votre solution (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

La préversion d’IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec les fonctionnalités d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce guide de démarrage rapide vous montre comment utiliser C# (avec .NET) pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide de démarrage rapide, vous devez installer .NET Core (2.x.x ou 3.x.x) sur votre ordinateur de développement. Vous pouvez télécharger votre version préférée du kit SDK .NET Core pour plusieurs plateformes à partir de [Télécharger .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Vous pouvez vérifier la version de .NET présente sur votre ordinateur de développement en exécutant la commande suivante dans une fenêtre de terminal local : 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Dans ce guide de démarrage rapide, vous utilisez un exemple de capteur environnemental écrit en C# en tant qu’appareil IoT Plug-and-Play. Les instructions suivantes vous montrent comment installer et exécuter l’appareil :

1. Ouvrez une fenêtre de terminal dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) à cet emplacement :

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Cette fenêtre de terminal est à présent utilisée comme terminal de votre _appareil_. Accédez au dossier de votre dépôt cloné, puis accédez au dossier **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample**.

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Générez les packages nécessaires, puis exécutez l’exemple avec la commande suivante :

    ```cmd\sh
        dotnet run
    ```

1. Vous voyez des messages indiquant que l’appareil s’est correctement inscrit et attend des mises à jour du cloud. L’appareil est donc prêt à recevoir des commandes et des mises à jour de propriétés et a commencé à envoyer des données de télémétrie au hub. Ne fermez pas ce terminal. Vous en aurez besoin plus tard pour confirmer que les exemples de service ont bien fonctionné.

## <a name="run-the-sample-solution"></a>Exécuter l’exemple de solution

Dans ce guide de démarrage rapide, vous utilisez un exemple de solution IoT en C# pour interagir avec l’exemple d’appareil.

1. Ouvrez une autre fenêtre de terminal (votre terminal de _service_). Accédez au dossier de votre dépôt cloné, puis accédez au dossier **/azure-iot-samples-csharp/digitaltwin/Samples/service**.

1. Configurez la _chaîne de connexion au hub IoT_ et l’_ID d’appareil_ pour permettre au service de se connecter à ceux-ci :

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Lire une propriété

1. Quand vous avez connecté l’_appareil_ dans son terminal, vous avez vu le message suivant indiquant son état en ligne :

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Accédez au terminal de _service_ et utilisez les commandes suivantes pour exécuter l’exemple de lecture des informations de l’appareil :

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Faites défiler la sortie du terminal de _service_ jusqu’au composant `environmentalSensor`. Vous voyez que la propriété `state`, qui est utilisée pour indiquer si l’appareil est en ligne ou non, a été signalée comme ayant pour valeur _true_ :

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Mettre à jour une propriété accessible en écriture

1. Accédez au terminal de _service_ et définissez les variables suivantes pour définir la propriété à mettre à jour :
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Utilisez les commandes suivantes pour exécuter l’exemple de mise à jour de la propriété :

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. Accédez à votre terminal d’_appareil_. Vous voyez que l’appareil a reçu la mise à jour :

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Revenez à votre terminal de _service_, puis réexécutez les commandes ci-dessous pour obtenir les informations sur l’appareil et confirmer que la propriété a été mise à jour.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. Dans la sortie du terminal de _service_, sous le composant `environmentalSensor`, vous voyez que la valeur de luminosité mise à jour a été signalée. Remarque : l’appareil peut prendre un certain temps pour terminer la mise à jour. Vous pouvez répéter cette étape jusqu’à ce que l’appareil ait réellement traité la mise à jour de propriété.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Appeler une commande

1. Accédez au terminal de _service_ et définissez les variables suivantes pour définir la commande à appeler :
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Utilisez les commandes suivantes pour exécuter l’exemple d’appel de la commande :

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. La sortie du terminal de _service_ doit indiquer la confirmation suivante :

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Accédez au terminal de l’_appareil_. Vous voyez que la commande a été reconnue :

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil et interagir avec celui-ci](howto-develop-solution.md)
