---
title: Interagir avec un appareil IoT Plug-and-Play (préversion) connecté à une solution Azure IoT | Microsoft Docs
description: Utilisez Java pour vous connecter à un appareil IoT Plug-and-Play en préversion connecté à votre solution Azure IoT et pour interagir avec lui.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 217dfe125dcacae5d50645275b20421a23169cb9
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550870"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play en préversion connecté à votre solution (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

La préversion d’IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec les fonctionnalités d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce démarrage rapide vous montre comment utiliser Java pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide de démarrage rapide, vous avez besoin de Java SE 8 sur votre ordinateur de développement. Vous devez également installer Maven 3.

Pour plus d’informations sur la configuration de ces éléments, consultez [Préparer votre environnement de développement](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) dans le Microsoft Azure IoT device SDK pour Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Dans ce guide de démarrage rapide, vous utilisez un exemple de capteur environnemental écrit en Java en tant qu’appareil IoT Plug-and-Play. Les instructions suivantes vous montrent comment installer et exécuter l’appareil :

1. Ouvrez une fenêtre de terminal dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT Samples for Java](https://github.com/Azure-Samples/azure-iot-samples-java) à cet emplacement :

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Cette fenêtre de terminal est à présent utilisée comme terminal de votre _appareil_. Accédez au dossier de votre dépôt cloné, puis accédez au dossier **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample**. Installez les bibliothèques requises et générez l’application d’appareil simulé en exécutant la commande suivante :

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Exécutez la commande suivante pour exécuter l’exemple à partir du dossier de l’appareil.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Vous voyez des messages indiquant que l’appareil est connecté, qu’il effectue différentes étapes de configuration et qu’il attend des mises à jour de service, puis des journaux de télémétrie. L’appareil est donc prêt à recevoir des commandes et des mises à jour de propriétés et a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes. Ne fermez pas ce terminal. Vous en aurez besoin plus tard pour confirmer que les exemples de service ont bien fonctionné.

## <a name="run-the-sample-solution"></a>Exécuter l’exemple de solution

Dans ce guide de démarrage rapide, vous utilisez un exemple de solution IoT en Java pour interagir avec l’exemple d’appareil.

1. Ouvrez une autre fenêtre de terminal (votre terminal de _service_). Accédez au dossier de votre dépôt cloné, puis accédez au dossier **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample**.

1. Installez les bibliothèques requises et générez l’exemple de service en exécutant la commande suivante :

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configurez la _chaîne de connexion au hub IoT_ et l’_ID d’appareil_ pour permettre au service de se connecter à ceux-ci :

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Lire une propriété

1. Quand vous avez connecté l’_appareil_ dans son terminal, l’un des messages de sortie était le message suivant indiquant son état en ligne. La propriété `state`, qui est utilisée pour indiquer si l’appareil est en ligne ou non, a pour valeur _true_ :

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Accédez au terminal de _service_ et utilisez la commande suivante pour exécuter l’exemple de service de lecture des informations de l’appareil :

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Faites défiler la sortie du terminal de _service_ jusqu’au composant `environmentalSensor`. Vous pouvez constater que la propriété `state` est signalée comme ayant pour valeur _true_ :
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. Utilisez la commande suivante pour exécuter l’exemple de service de mise à jour de la propriété :

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. La sortie du terminal de _service_ présente les informations à jour sur l’appareil. Faites défiler jusqu’au composant `environmentalSensor` pour voir la nouvelle valeur de luminosité (42).

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Accédez à votre terminal d’_appareil_. Vous voyez que l’appareil a reçu la mise à jour :

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Revenez à votre terminal de _service_, puis réexécutez la commande ci-dessous pour obtenir les informations sur l’appareil et confirmer que la propriété a été mise à jour.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. Dans la sortie du terminal de _service_, sous le composant `environmentalSensor`, vous voyez que la valeur de luminosité mise à jour a été signalée. Remarque : l’appareil peut prendre un certain temps pour terminer la mise à jour. Vous pouvez répéter cette étape jusqu’à ce que l’appareil ait réellement traité la mise à jour de propriété.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Appeler une commande

1. Accédez au terminal de _service_ et définissez les variables suivantes pour définir la commande à appeler :
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Utilisez la commande suivante pour exécuter l’exemple de service d’appel de la commande :

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. La sortie du terminal de _service_ doit indiquer la confirmation suivante :

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Accédez au terminal de l’_appareil_. Vous voyez que la commande a été reconnue :

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil et interagir avec celui-ci](howto-develop-solution.md)
