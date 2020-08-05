---
title: Connecter un exemple de code d’appareil Node.js IoT Plug-and-Play en préversion à Azure IoT Hub | Microsoft Docs
description: À l’aide de Node.js, créez et exécutez l’exemple de code d’appareil IoT Plug-and-Play en préversion qui se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 00a748c3c372f1980042cff201edec720587a511
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422553"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Démarrage rapide : Connecter un exemple d’application d’appareil IoT Plug-and-Play en préversion à IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ce démarrage rapide montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT, et comment utiliser l’outil Explorateur Azure IoT pour afficher la télémétrie qu’il envoie. L’exemple d’application est écrit en Node.js et est inclus dans le kit Azure IoT device SDK pour Node.js. Un créateur de solutions peut utiliser l’outil Explorateur Azure IoT pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir besoin d’examiner du code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous avez besoin de Node.js sur votre ordinateur de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Explorateur Azure IoT

Pour interagir avec l’exemple d’appareil dans la deuxième partie de ce démarrage rapide, vous utilisez l’outil **Explorateur Azure IoT**. [Téléchargez et installez la dernière version de l’Explorateur Azure IoT](./howto-use-iot-explorer.md) pour votre système d’exploitation.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce démarrage rapide :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Vous pouvez également utiliser l’outil Explorateur Azure IoT pour rechercher la chaîne de connexion du hub IoT.

Exécutez la commande suivante pour obtenir la _chaîne de connexion_ à l’appareil que vous avez ajouté au hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce démarrage rapide :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Téléchargement du code

Dans ce guide de démarrage rapide, vous préparez un environnement de développement pour cloner et générer le kit Azure IoT Hub Device SDK pour Node.js.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [SDK Microsoft Azure IoT pour for Node.js](https://github.com/Azure/azure-iot-sdk-node) à cet emplacement :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installer les bibliothèques nécessaires

Vous utilisez le kit de développement logiciel (SDK) de l’appareil pour créer l’exemple de code inclus. L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Dans une fenêtre de terminal locale, accédez au dossier de votre dépôt cloné, puis au dossier */azure-iot-sdk-node/device/samples/pnp*. Exécutez ensuite la commande suivante pour installer les bibliothèques requises :

    ```cmd/sh
    npm install
    ```

1. Configurez la variable d’environnement avec la chaîne de connexion d’appareil que vous avez notée précédemment :

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Exécuter l’exemple d’appareil

Ouvrez le fichier _simple_thermostat.js_. Dans ce fichier, vous pouvez voir comment :

1. Importer les interfaces nécessaires.
1. Écrire un gestionnaire de mise à jour de propriété et un gestionnaire de commandes.
1. Gérer les correctifs de propriété souhaités et envoyer les données de télémétrie.
1. (Facultatif) Provisionner votre appareil à l’aide du service de provisionnement des appareils (DPS) Azure.

Dans la fonction principale, vous pouvez voir comment tout cela se résume :

1. Créez l’appareil à partir de votre chaîne de connexion ou provisionnez-le à l’aide de DPS.
1. Utilisez l’option **modelID** pour spécifier le modèle d’appareil IoT Plug-and-Play.
1. Activez le gestionnaire de commandes.
1. Envoyez les données de télémétrie de l’appareil à votre hub.
1. Récupérez les jumeaux d’appareil et mettez à jour les propriétés signalées.
1. Activez le gestionnaire de mise à jour de propriété souhaité.

Exécutez l’exemple d’application pour simuler un appareil IoT Plug-and-Play qui envoie des données de télémétrie à votre hub IoT. Pour exécuter l’exemple d’application, utilisez la commande suivante :

```cmd\sh
node simple_thermostat.js
```

Vous voyez la sortie suivante, qui indique que l’appareil a commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés.

![Messages de confirmation de l’appareil](media/quickstart-connect-device-node/device-confirmation-node.png)

Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

Une fois l’exemple de client d’appareil démarré, utilisez l’outil Explorateur Azure IoT pour vérifier qu’il fonctionne.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Interagir avec un appareil IoT Plug-and-Play en préversion connecté à votre solution](quickstart-service-node.md)
