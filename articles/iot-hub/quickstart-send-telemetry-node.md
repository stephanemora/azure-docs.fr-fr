---
title: 'Démarrage rapide : Envoyer des données de télémétrie à Azure IoT (Node.js)'
description: Dans ce démarrage rapide, vous exécutez deux exemples d’applications Node.js pour envoyer des données de télémétrie simulées à un IoT Hub et lire les données de télémétrie provenant de l’IoT Hub à traiter dans le cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 8b99cf08e0e47ab99deb443a0fefee6ba0d61a88
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675383"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT et les lire avec une application back-end (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 Dans ce guide de démarrage rapide, vous envoyez la télémétrie d’une application d’appareil simulé via Azure IoT Hub à une application back-end pour traitement. IoT Hub est un service Azure qui vous permet de traiter de gros volumes de données de télémétrie envoyées par vos appareils IoT dans le cloud à des fins de stockage ou de traitement. Ce guide de démarrage rapide utilise deux applications Node.js prédéfinies : une pour envoyer la télémétrie et l’autre pour lire cette télémétrie à partir du hub. Avant d’exécuter ces deux applications, vous créez un IoT Hub et inscrivez un appareil auprès du concentrateur.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org). Si vous utilisez Azure Cloud Shell, ne mettez pas à jour la version installée de Node.js. Azure Cloud Shell a déjà la dernière version de Node.js.

* [Un exemple de projet Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* Le port 8883 ouvert dans votre pare-feu. L’exemple d’appareil de ce guide de démarrage rapide utilise le protocole MQTT, lequel communique sur le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d'informations sur les différentes façons de contourner ce problème, consultez [Se connecter à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Vous pouvez vérifier la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Ajouter une extension Azure IoT

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et du service IoT Hub Device Provisioning (DPS) à Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour créer l’identité d’appareil.

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **MyNodeDevice** : il s’agit du nom de l’appareil que vous inscrivez. Il est recommandé d’utiliser **MyNodeDevice** comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devrez utiliser ce nom tout au long de cet article et mettre à jour le nom de l'appareil dans les exemples d'application avant de les exécuter.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Exécutez la commande suivante dans Azure Cloud Shell pour obtenir la _chaîne de connexion d’appareil_ pour celui que vous venez d’inscrire :

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Notez la chaîne de connexion à l’appareil, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce guide de démarrage rapide.

1. Vous avez également besoin d’une _chaîne de connexion de service_ pour activer l’application back-end afin de vous connecter à votre IoT Hub et récupérer les messages. La commande suivante récupère la chaîne de connexion de service correspondant à votre hub IoT :

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Notez la chaîne de connexion de service, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce guide de démarrage rapide. La chaîne de connexion de service est différente de la chaîne de connexion d’appareil que vous avez notée à l’étape précédente.

## <a name="send-simulated-telemetry"></a>Envoyer des données de télémétrie simulée

L’application d’appareil simulé se connecte à un point de terminaison spécifique de l’appareil sur votre IoT Hub et envoie les données de télémétrie simulée (température et humidité).

1. Ouvrez une fenêtre de votre terminal local, accédez au dossier racine de l’exemple de projet Node.js. Ensuite, accédez au dossier **iot-hub\Quickstarts\simulated-device**.

1. Utilisez un éditeur de texte pour ouvrir le fichier **SimulatedDevice.js**.

    Remplacez la valeur de la variable `connectionString` par la chaîne de connexion d’appareil que vous avez notée précédemment. Enregistrez ensuite les changements apportés à **SimulatedDevice.js**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises et exécuter l’application d’appareil simulé :

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    La capture d’écran suivante présente la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie à votre IoT Hub :

    ![Exécuter l’appareil simulé](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lire les données de télémétrie envoyées par votre hub

L’application back-end se connecte au point de terminaison **Événements** du service sur votre IoT Hub. L’application reçoit les messages appareil-à-cloud envoyés à partir de votre appareil simulé. Une application back-end IoT Hub s’exécute généralement dans le cloud pour recevoir et traiter les messages appareil-à-cloud.

1. Ouvrez une autre fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Node.js. Puis, accédez au dossier **iot-hub\Quickstarts\read-d2c-messages**.

1. Ouvrez le fichier **ReadDeviceToCloudMessages.js** dans un éditeur de texte de votre choix.

    Remplacez la valeur de la variable `connectionString` par la chaîne de connexion de service que vous avez notée précédemment. Enregistrez ensuite les changements apportés à **ReadDeviceToCloudMessages.js**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises et exécuter l’application back-end :

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    La capture d’écran suivante présente la sortie lorsque l’application back-end reçoit les données de télémétrie envoyées par l’appareil simulé au concentrateur :

    ![Exécuter l’application back-end](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil, envoyé des données de télémétrie simulée au hub à l’aide d’une application Node.js et lu les données de télémétrie à partir du hub à l’aide d’une application back-end simple.

Pour savoir comment contrôler votre appareil simulé à partir d’une application back-end, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Démarrage rapide : Contrôler un appareil connecté à un hub IoT](quickstart-control-device-node.md)
