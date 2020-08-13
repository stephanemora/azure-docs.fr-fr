---
title: Connecter l’exemple de code d’appareil de composant Node.js IoT Plug-and-Play en préversion à IoT Hub | Microsoft Docs
description: Créez et exécutez l’exemple de code d’appareil Node.js IoT Plug-and-Play en préversion qui utilise plusieurs composants et se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-javascript
ms.openlocfilehash: d26179ab82f29ce8f937f5b444463c1308d92047
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904052"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Tutoriel : Connecter un exemple d’application d’appareil multicomposant IoT Plug-and-Play en préversion à IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ce tutoriel vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play avec des composants et une interface racine, comment le connecter à votre hub IoT, et comment utiliser l’outil Explorateur Azure IoT pour voir les informations qu’il envoie au hub. L’exemple d’application est écrit pour Node.js et est inclus dans le kit Azure IoT Hub Device SDK pour Node.js. Un créateur de solutions peut utiliser l’outil Explorateur Azure IoT pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir besoin d’examiner le code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin de Node.js sur votre machine de développement. Vous pouvez télécharger la dernière version recommandée pour plusieurs plateformes à partir de [nodejs.org](https://nodejs.org).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Explorateur Azure IoT

Pour interagir avec l’exemple d’appareil dans la deuxième partie de ce tutoriel, vous utilisez l’outil **Explorateur Azure IoT**. [Téléchargez et installez la dernière version de l’Explorateur Azure IoT](./howto-use-iot-explorer.md) pour votre système d’exploitation.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce tutoriel :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Vous pouvez également utiliser l’outil Explorateur Azure IoT pour rechercher la chaîne de connexion du hub IoT.

Exécutez la commande suivante pour obtenir la _chaîne de connexion d’appareil_ pour l’appareil que vous avez ajouté au hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce tutoriel :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Téléchargement du code

Dans ce tutoriel, vous préparez un environnement de développement que vous pouvez utiliser pour cloner et générer le SDK Azure IoT Hub Device pour Node.js.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [SDK Microsoft Azure IoT pour for Node.js](https://github.com/Azure/azure-iot-sdk-node) à cet emplacement :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Cette opération peut prendre plusieurs minutes.

## <a name="install-required-libraries"></a>Installer les bibliothèques nécessaires

Vous utilisez le kit de développement logiciel (SDK) de l’appareil pour créer l’exemple de code inclus. L’application que vous créez simule un appareil Plug-and-Play avec plusieurs composants et une interface racine qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Dans une fenêtre de terminal locale, accédez au dossier de votre dépôt cloné, puis au dossier */azure-iot-sdk-node/device/samples/pnp*. Exécutez ensuite la commande suivante pour installer les bibliothèques requises :

```cmd/sh
npm install
```

Ceci va installer les fichiers npm appropriés nécessaires pour exécuter les exemples dans le dossier.

1. Configurez la variable d’environnement avec la chaîne de connexion d’appareil que vous avez notée précédemment :

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>Vérifier le code

Accédez au dossier *Azure-IOT-SDK-node\device\samples\pnp*.

Le dossier *Azure-IOT-SDK-node\device\samples\pnp* contient l’exemple de code pour l’appareil contrôleur de température IoT Plug-and-Play.

Le code figurant dans le fichier *pnpTemperatureController.js* implémente un appareil contrôleur de température IoT Plug-and-Play. Le modèle implémenté par cet exemple utilise [plusieurs composants](concepts-components.md). Le [fichier de modèle DTDL (Digital Twins Definition Language) pour l’appareil de contrôle de température](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) définit la télémétrie, les propriétés et les commandes implémentées par l’appareil.

Ouvrez le fichier *pnpTemperatureController.js* dans un éditeur de code de votre choix. L’exemple de code montre comment :

1. Définir le `modelId` qui est le DTMI pour l’appareil à implémenter. Ce DTMI est défini par l’utilisateur et doit correspondre au DTMI du [modèle DTDL du contrôleur de température](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

2. Implémenter les composants définis dans le modèle DTDL du contrôleur de température. Les composants d’un contrôleur de température réel doivent implémenter ces deux interfaces. Ces deux interfaces sont déjà publiées dans un dépôt central. Dans cet exemple, les deux interfaces sont :
  - Thermostat
  - Informations sur l’appareil développées par Azure

3. Définit les noms des composants. Cet exemple a deux thermostats et un composant d’informations sur l’appareil.

4. Définit un nom de commande. Il s’agit des commandes auxquelles l’appareil répond.

5. Définit la constante `serialNumber`. `serialNumber` est fixe pour un appareil donné.

6. Définit les gestionnaires de commandes.

7. Définit les fonctions pour envoyer des réponses aux commandes.

8. Définit des fonctions helper pour journaliser les demandes de commandes.

9. Définit une fonction helper pour créer les propriétés.

10. Définit un écouteur pour les mises à jour des propriétés.

11. Définit une fonction pour envoyer de la télémétrie depuis cet appareil. Les thermostats et le composant racine envoient de la télémétrie. Cette fonction reçoit le nom du composant comme paramètre.

12. Définissez une fonction `main` qui :

    1. Utilise le SDK d’appareil pour créer un client d’appareil et se connecter à votre hub IoT. L’appareil fournit le `modelId` pour que IoT Hub puisse identifier l’appareil en tant qu’appareil IoT Plug-and-Play.

    1. Commence à écouter les demandes de commande en utilisant la fonction `onDeviceMethod`. La fonction configure un écouteur pour les demandes de commande provenant du service :
        - Le DTDL de l’appareil définit les commandes `reboot` et `getMaxMinReport`.
        - La fonction `commandHandler` définit la façon dont l’appareil répond à une commande.

    1. Commence à envoyer de la télémétrie en utilisant `setInterval` et `sendTelemetry`.

    1. Utilise la fonction `helperCreateReportedPropertiesPatch` pour créer les propriétés et les `updateComponentReportedProperties` pour mettre à jour les propriétés.

    1. Utilise `desiredPropertyPatchListener` pour être à l’écoute des mises à jour de propriétés.

    1. Désactive tous les écouteurs et toutes les tâches, puis quitte la boucle quand vous appuyez sur **Q** ou **q**.

Maintenant que vous avez vu le code, utilisez la commande suivante pour exécuter l’exemple :

```cmd\sh
node pnpTemperatureController.js
```

Vous voyez la sortie suivante, qui indique que l’appareil a commencé à envoyer des données de télémétrie au hub et est maintenant prêt à recevoir des commandes et des mises à jour de propriétés.

![Messages de confirmation de l’appareil](media/tutorial-multiple-components-node/multiple-component.png)

Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

Une fois l’exemple de client d’appareil démarré, utilisez l’outil Explorateur Azure IoT pour vérifier qu’il fonctionne.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment connecter un appareil IoT Plug-and-Play avec des composants à un hub IoT. Pour découvrir plus d’informations sur les modèles d’appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide du développeur pour la modélisation avec la préversion d’IoT Plug-and-Play](concepts-developer-guide.md)
