---
title: Démarrage rapide pour envoyer la télémétrie des appareils à Azure IoT Hub (Node.js)
description: Dans ce démarrage rapide, vous utilisez le Kit de développement logiciel (SDK) Azure IoT Hub Device pour Node.js afin d’envoyer les données de télémétrie d’un appareil à un hub IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197807"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT (Node.js)

**S’applique à** : [Développement d’applications pour appareils](about-iot-develop.md#device-application-development)

Dans ce démarrage rapide, vous allez apprendre un workflow de base relatif au développement d’applications pour appareils IoT. Vous utilisez l’interface de ligne de commande Azure pour créer un hub Azure IoT et un appareil simulé, puis vous utilisez le Kit de développement logiciel (SDK) Azure IoT Node.js pour accéder à l’appareil et envoyer la télémétrie au hub.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Azure CLI. Vous pouvez exécuter toutes les commandes dans ce démarrage rapide à l’aide de Azure Cloud Shell, un interpréteur de commandes CLI interactif qui s’exécute dans votre navigateur. Si vous utilisez Cloud Shell, vous n’avez rien à installer. Si vous préférez installer et utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite au minimum Azure CLI version 2.0.76. Pour déterminer la version, exécutez la commande az--version. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Si vous utilisez Azure Cloud Shell, ne mettez pas à jour la version installée de Node.js. Azure Cloud Shell a déjà la dernière version de Node.js.

    Vérifiez la version actuelle de Node.js sur votre machine de développement à l’aide de la commande suivante :

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Utiliser le Kit de développement logiciel (SDK) Node.js pour envoyer des messages
Dans cette section, vous allez utiliser le Kit de développement logiciel (SDK) Node.js pour envoyer des messages de votre appareil simulé à votre hub IoT. 

1. Ouvrez une nouvelle fenêtre de terminal. Vous allez utiliser ce terminal pour installer le Kit de développement logiciel (SDK) Node.js et travailler avec des exemples de code Node.js. Vous devez maintenant avoir deux terminaux ouverts : celui que vous venez d’ouvrir pour travailler avec Node.js et l’interpréteur de commandes CLI que vous avez utilisé dans les sections précédentes pour entrer des commandes Azure CLI. 

1. Copiez les [exemples d’appareils du Kit de développement logiciel (SDK) Azure IoT Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) sur votre ordinateur local :

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Accédez au répertoire *azure-iot-sdk-node/device/samples* :

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Installez le Kit de développement logiciel (SDK) Azure IoT Node.js et les dépendances nécessaires :

    ```console
    npm install
    ```
    Cette commande installe les dépendances appropriées comme spécifié dans le fichier *package.json* du répertoire des exemples d’appareils.

1. Définissez la chaîne de connexion de l’appareil comme variable d’environnement appelée `DEVICE_CONNECTION_STRING`. La valeur de chaîne à utiliser est la chaîne que vous avez obtenue dans la section précédente après la création de votre appareil Node.js simulé. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après la chaîne de connexion.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. Dans votre interpréteur de commandes CLI ouvert, exécutez la commande [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) pour commencer à surveiller les événements sur votre appareil IoT simulé.  Les messages d’événement seront imprimés dans le terminal au fur et à mesure de leur arrivée.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Dans votre terminal Node.js, exécutez le code du fichier d’exemple *simple_sample_device.js* installé. Ce code accède à l’appareil IoT simulé et envoie un message au hub IoT.

    Pour exécuter l’exemple Node.js à partir du terminal :
    ```console
    node ./simple_sample_device.js
    ```

    Si vous le souhaitez, vous pouvez exécuter le code Node.js à partir de l’exemple dans votre environnement de développement intégré (IDE) JavaScript :
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

Lorsque le code Node.js envoie un message simulé de télémétrie de votre appareil au hub IoT, le message s’affiche dans votre interpréteur de commandes CLI qui analyse les événements :

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

Votre appareil est maintenant connecté en toute sécurité et envoie des données de télémétrie à Azure IoT Hub.

## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez plus besoin des ressources Azure créées dans ce démarrage rapide, vous pouvez utiliser Azure CLI pour les supprimer.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

Pour supprimer un groupe de ressources par nom :
1. Exécutez la commande [az group delete](/cli/azure/group#az-group-delete). Cette commande supprime le groupe de ressources, le hub IoT et l’inscription de l’appareil que vous avez créés.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Exécutez la commande [az group list](/cli/azure/group#az-group-list) pour confirmer la suppression du groupe de ressources.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris un workflow de base d’application Azure IoT pour connecter de façon sécurisée un appareil au cloud et envoyer des données de télémétrie appareil-à-cloud. Vous avez utilisé l’interface de ligne de commande Azure pour créer un hub IoT et un appareil simulé, puis avez utilisé le Kit de développement logiciel (SDK) Azure IoT Node.js pour accéder à l’appareil et envoyer la télémétrie au hub. 

L’étape suivante consiste à explorer le Kit de développement logiciel (SDK) Azure IoT Node.js à l’aide d’exemples d’applications.

- [Autres exemples Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) : Ce répertoire contient plus d’exemples du référentiel du Kit de développement logiciel (SDK) Node.js pour présenter des scénarios IoT Hub.