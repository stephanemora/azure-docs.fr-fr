---
title: Connecter l’exemple de code d’appareil IoT Plug-and-Play en préversion à IoT Hub | Microsoft Docs
description: À l’aide de Java, créez et exécutez l’exemple de code d’appareil IoT Plug-and-Play en préversion qui se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e1454a197620cacaa8c8303e8f36a8d4a87aec00
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531307"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion à IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ce démarrage rapide vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT et comment utiliser l’outil Azure IoT Explorer pour afficher les informations qu’il envoie au hub. L’exemple d’application est écrit en Java et est fourni dans le cadre de la collection d’exemples Azure IoT pour Java. Un développeur de solutions peut utiliser l’outil Azure IoT Explorer pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir à afficher de code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide de démarrage rapide, vous avez besoin de Java SE 8 sur votre ordinateur de développement. Vous devez également installer Maven 3.

Pour plus d’informations sur la configuration de ces éléments, consultez [Préparer votre environnement de développement](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) dans le Microsoft Azure IoT device SDK pour Java.

### <a name="install-the-azure-iot-explorer"></a>Installer l’explorateur Azure IoT

Téléchargez et installez la dernière version de l’**explorateur Azure IoT** à partir de la page [référentiel](https://github.com/Azure/azure-iot-explorer/releases) de l’outil, en sélectionnant le fichier . msi sous « Assets » pour obtenir la mise à jour la plus récente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Dans ce guide de démarrage rapide, vous préparez un environnement de développement pour cloner et générer les exemples Azure IoT pour Java.

Ouvrez une fenêtre de terminal dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT Samples for Java](https://github.com/Azure-Samples/azure-iot-samples-java) à cet emplacement :

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Cette opération peut prendre plusieurs minutes.

## <a name="build-the-code"></a>Générer le code

Vous utilisez l’exemple de code cloné pour générer une application simulant un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Dans une fenêtre de terminal local, accédez au dossier de votre dépôt cloné, puis accédez au dossier **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample**. Ensuite, exécutez la commande suivante pour installer les bibliothèques requises et générer l’application d’appareil simulé :

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Exécutez un exemple d’application pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Pour exécuter l’exemple d’application, utilisez la commande suivante :

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Vous voyez des messages indiquant que l’appareil est connecté, qu’il effectue différentes étapes de configuration et qu’il attend des mises à jour de service, puis des journaux de télémétrie. L’appareil est donc prêt à recevoir des commandes et des mises à jour de propriétés et a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Pour vérifier que l’outil peut lire les définitions de modèle d’interface à partir de votre appareil, sélectionnez **Paramètres**. Dans le menu Paramètres, **Sur l’appareil connecté** peut déjà apparaître dans les configurations Plug-and-Play. Si ce n’est pas le cas, sélectionnez **+ Ajouter une source de définition de module**, puis **Sur l’appareil connecté** pour l’ajouter.

1. Dans la page de vue d’ensemble **Appareils**, recherchez l’identité d’appareil que vous avez créée précédemment. L’application de l’appareil étant toujours en cours d’exécution à l’invite de commandes, vérifiez que l’**État de connexion** de l’appareil dans Azure IoT Explorer indique _Connecté_ (dans le cas contraire, appuyez sur **Actualiser** jusqu’à ce que cet état apparaisse). Sélectionnez l’appareil pour afficher plus de détails.

1. Développez l’interface avec l’ID **urn:java_sdk_sample:EnvironmentalSensor:1** pour révéler l’interface et les primitives IoT Plug-and-Play (propriétés, commandes et données de télémétrie).

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil IoT Plug-and-Play (préversion) et interagir avec celui-ci](howto-develop-solution.md)
