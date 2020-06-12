---
title: Tutoriel – Connecter une application cliente Node.js générique à Azure IoT Central | Microsoft Docs
description: Ce tutoriel explique comment, en tant que développeur d’appareils, connecter un appareil exécutant une application cliente Node.js à votre application Azure IoT Central. Vous créez un modèle d’appareil en important un modèle de capacité d’appareil et vous ajoutez des vues qui vous permettent d’interagir avec un appareil connecté.
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: 65f441425113d89010cc2d282758c5a042be9300
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417903"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Cet article s’applique aux créateurs de solutions et aux développeurs d’appareils.*

Ce tutoriel explique comment, en tant que développeur d’appareils, connecter une application cliente Node.js à votre application Azure IoT Central. L’application Node.js simule le comportement d’un appareil capteur environnemental. Vous utilisez un exemple de _modèle de capacité d’appareil_ pour créer un _modèle d’appareil_  dans IoT Central. Vous ajoutez des vues au modèle d’appareil pour permettre à un opérateur d’interagir avec un appareil.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Importer un modèle de capacité d’appareil pour créer un modèle d’appareil.
> * Ajouter des vues par défaut et personnalisées à un modèle d’appareil.
> * Publier un modèle d’appareil et ajouter un appareil réel à votre application IoT Central.
> * Créer et exécuter le code d’appareil Node.js et veiller à ce qu’il se connecte à votre application IoT Central.
> * Afficher la télémétrie simulée envoyée à partir de l’appareil.
> * Utiliser une vue pour gérer les propriétés de l’appareil.
> * Appeler des commandes synchrones et asynchrones pour contrôler l’appareil.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

* Une application Azure IoT Central créée avec le modèle **Application personnalisée**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Une machine de développement où [Node.js](https://nodejs.org/) version 10.0.0 ou ultérieure est installé. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Les instructions de ce tutoriel supposent que vous exécutez la commande **node** à l’invite de commandes Windows. Toutefois, vous pouvez utiliser Node.js sur de nombreux autres systèmes d’exploitation.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Création d’une application Node.js

Les étapes suivantes vous montrent comment créer une application cliente Node.js qui se connecte à l’appareil réel que vous avez ajouté à l’application. L’application Node.js simule le comportement d’un appareil réel.

1. Dans votre environnement de ligne de commande, accédez au dossier `environmental-sensor` que vous avez créé auparavant.

1. Pour initialiser votre projet Node.js et installer les dépendances requises, exécutez les commandes suivantes – Acceptez toutes les options par défaut lorsque vous exécutez `npm init` :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Créez un fichier appelé **environmentalSensor.js** dans le dossier `environmental-sensor`.

1. Ajoutez les instructions `require` suivantes au début du fichier **environmentalSensor.js** :

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Ajoutez les déclarations de variable ci-après au fichier :

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Mettez à jour les espaces réservés `{your Scope ID}`, `{your Device ID}` et `{your Primary Key}` avec les valeurs que vous avez notées précédemment. Dans cet exemple, vous initialisez `targetTemperature` à zéro, mais vous pourriez utiliser la valeur actuelle lue sur l’appareil ou une valeur du jumeau d’appareil.

1. Pour envoyer la télémétrie simulée à votre application Azure IoT Central, ajoutez la fonction suivante au fichier :

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Les noms des éléments de télémétrie (`temp` et `humid`) doivent correspondre aux noms utilisés dans le modèle d’appareil.

1. Pour envoyer des propriétés de jumeau d’appareil à votre application Azure IoT Central, ajoutez la fonction suivante à votre fichier :

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central utilise les jumeaux d’appareil pour synchroniser les valeurs de propriété entre l’appareil et l’application IoT Central. Les valeurs de propriété d’appareil utilisent les propriétés signalées du jumeau d’appareil. Les propriétés inscriptibles utilisent à la fois les propriétés souhaitées et signalées du jumeau d’appareil.

1. Pour définir et gérer les propriétés inscriptibles auxquelles votre appareil répond, ajoutez le code suivant :

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Quand l’opérateur définit une propriété inscriptible dans l’application IoT Central, l’application utilise une propriété souhaitée du jumeau d’appareil pour envoyer la valeur à l’appareil. L’appareil répond alors en utilisant une propriété signalée du jumeau d’appareil. Quand IoT Central reçoit la valeur de la propriété signalée, il met à jour la vue de propriété avec l’état **synchronisé**.

    Les noms des propriétés (`name` et `brightness`) doivent correspondre aux noms utilisés dans le modèle d’appareil.

1. Ajoutez le code suivant pour gérer les commandes envoyées à partir de l’application IoT Central :

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    Les noms des commandes (`blink`, `turnon`, `turnoff` et `rundiagnostics`) doivent correspondre aux noms utilisés dans le modèle d’appareil.

    Actuellement, IoT Central n’utilise pas le schéma de réponse défini dans le modèle de capacité d’appareil. Pour une commande synchrone, la charge utile de réponse peut être n’importe quelle valeur JSON valide. Pour une commande asynchrone, l’appareil doit retourner immédiatement une réponse 202, suivie d’une mise à jour de propriété signalée une fois le travail terminé. Le format de la mise à jour de propriété signalée est le suivant :

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Un opérateur peut afficher la charge utile de réponse dans l’historique des commandes.

1. Ajoutez le code ci-après pour effectuer la connexion à Azure IoT Central et raccorder les fonctions dans le code client :

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Exécuter votre application Node.js

Pour démarrer l’application cliente d’appareil, exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd/sh
node environmentalSensor.js
```

Vous pouvez voir que l’appareil se connecte à votre application Azure IoT Central et commence à envoyer des données de télémétrie :

![Exécution de l’application cliente](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Vous pouvez voir comment l’appareil répond aux commandes et aux mises à jour de propriétés :

![Observer l’application cliente](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Étapes suivantes

En tant que développeur d’appareils, maintenant que vous avez appris les bases de la création d’un appareil à l’aide de Node.js, les prochaines étapes suggérées sont les suivantes :

* Découvrez comment connecter un appareil réel à IoT Central dans l’article sur les procédures [Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central](./howto-connect-devkit.md).
* Pour en savoir plus sur le rôle des modèles d’appareils quand vous implémentez votre code d’appareil, consultez [Présentation des modèles d’appareils](./concepts-device-templates.md).
* Lisez [Se connecter à Azure IoT Central](./concepts-get-connected.md) pour en savoir plus sur la façon d’inscrire des appareils auprès d’IoT Central et sur la manière dont IoT Central sécurise les connexions des appareils.

Si vous préférez suivre l’ensemble des tutoriels IoT Central et en savoir plus sur la création d’une solution IoT Central, consultez :

> [!div class="nextstepaction"]
> [Créer un modèle d’appareil de passerelle](./tutorial-define-gateway-device-type.md)
