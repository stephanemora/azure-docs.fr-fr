---
title: Connecter l’exemple de code d’appareil IoT Plug-and-Play en préversion à IoT Hub | Microsoft Docs
description: À l’aide de node.js, créez et exécutez l’exemple de code d’appareil IoT Plug-and-Play en préversion qui se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964785"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion à IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ce démarrage rapide vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT et comment utiliser l’outil Azure IoT Explorer pour afficher les informations qu’il envoie au hub. L’exemple d’application est écrit pour Node.js et est inclus dans le kit Azure IoT Hub Device SDK pour Node.js. Un développeur de solutions peut utiliser l’outil Azure IoT Explorer pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir à afficher de code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide de démarrage rapide, vous avez besoin de Node.js sur votre ordinateur de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Installer l’explorateur Azure IoT

Téléchargez et installez la dernière version de l’**explorateur Azure IoT** à partir de la page [référentiel](https://github.com/Azure/azure-iot-explorer/releases) de l’outil, en sélectionnant le fichier . msi sous « Assets » pour obtenir la mise à jour la plus récente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub (prenez-en note pour l’utiliser plus tard) :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Dans ce guide de démarrage rapide, vous préparez un environnement de développement pour cloner et générer le kit Azure IoT Hub Device SDK pour Node.js.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [SDK Microsoft Azure IoT pour for Node.js](https://github.com/Azure/azure-iot-sdk-node) à cet emplacement :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Cette opération peut prendre plusieurs minutes.

## <a name="install-required-libraries"></a>Installer les bibliothèques nécessaires

Vous utilisez le kit de développement logiciel (SDK) de l’appareil pour créer l’exemple de code inclus. L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Dans une fenêtre de terminal local, accédez au dossier de votre dépôt cloné et accédez au dossier **/azure-iot-sdk-node/digitaltwins/samples/device/javascript**. Exécutez ensuite la commande suivante pour installer les bibliothèques requises :

    ```cmd/sh
    npm install
    ```
1. Configurer la _chaîne de connexion de l’appareil_ :

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Exécutez un exemple d’application dans le kit de développement logiciel (SDK) pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Pour exécuter l’exemple d’application, utilisez la commande suivante :

```cmd\sh
    node sample_device.js
```

Vous voyez la sortie suivante, qui indique que l’appareil a commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés.

   ![Messages de confirmation de l’appareil](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Pour vérifier que l’outil peut lire les définitions de modèle d’interface à partir de votre appareil, sélectionnez **Paramètres**. Dans le menu Paramètres, **Sur l’appareil connecté** peut déjà apparaître dans les configurations Plug-and-Play. Si ce n’est pas le cas, sélectionnez **+ Ajouter une source de définition de module**, puis **Sur l’appareil connecté** pour l’ajouter.

1. Dans la page de vue d’ensemble **Appareils**, recherchez l’identité d’appareil que vous avez créée précédemment. L’application de l’appareil étant toujours en cours d’exécution à l’invite de commandes, vérifiez que l’**État de connexion** de l’appareil dans Azure IoT Explorer indique _Connecté_ (dans le cas contraire, appuyez sur **Actualiser** jusqu’à ce que cet état apparaisse). Sélectionnez l’appareil pour afficher plus de détails.

1. Développez l’interface avec l’ID **urn:contoso:com:EnvironmentalSensor:1** pour révéler l’interface et les primitives IoT Plug-and-Play (propriétés, commandes et données de télémétrie).

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil IoT Plug-and-Play (préversion) et interagir avec celui-ci](howto-develop-solution.md)
