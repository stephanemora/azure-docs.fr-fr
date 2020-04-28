---
title: Connecter un appareil Raspberry Pi à la solution de supervision à distance - Node.js - Azure| Microsoft Docs
description: Explique comment connecter un appareil Raspberry Pi à l’accélérateur de solution de supervision à distance avec une application écrite en Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt
ms.openlocfilehash: 9335c45688752ea41801e988157740f4170cfcb4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683945"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Connecter votre appareil Raspberry Pi à l’accélérateur de solution de supervision à distance (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce tutoriel vous montre comment connecter un appareil réel à l’accélérateur de solution de supervision à distance. Dans ce didacticiel, vous utilisez Node.js, qui est une bonne option pour les environnements avec des contraintes minimales en ressources.

Si vous préférez simuler un appareil, consultez [Créer et tester un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Matériel requis

Un ordinateur de bureau permettant de vous connecter à distance à la ligne de commande sur le Raspberry Pi.

[Starter Kit Microsoft IoT pour Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou composants équivalents. Ce didacticiel utilise les éléments suivants du kit :

- Raspberry Pi 3
- Carte MicroSD (avec NOOBS)
- Un câble mini USB
- Un câble Ethernet

### <a name="required-desktop-software"></a>Logiciels de bureau requis

Vous devez installer le client SSH sur votre ordinateur de bureau afin de pouvoir accéder à distance à la ligne de commande sur le Raspberry Pi.

- Windows n’inclut pas de client SSH. Nous vous recommandons d’utiliser [PuTTY](https://www.putty.org/).
- La plupart des distributions Linux et Mac OS incluent l’utilitaire de ligne de commande SSH. Pour plus d’informations, consultez [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (Utilisation de SSH avec Linux ou Mac OS).

### <a name="required-raspberry-pi-software"></a>Logiciels Raspberry Pi requis

Si ce n’est déjà fait, installez Node.js version 4.0.0 ou ultérieure sur votre appareil Raspberry Pi. Les étapes suivantes vous montrent comment installer Node.js v6 sur votre appareil Raspberry Pi :

1. Connectez-vous à votre appareil Raspberry Pi avec `ssh`. Pour plus d’informations, consultez [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) sur le [site web de Raspberry Pi](https://www.raspberrypi.org/).

1. Pour mettre à jour votre Raspberry Pi, utilisez la commande suivante :

    ```sh
    sudo apt-get update
    ```

1. Pour supprimer toute installation existante de Node.js de votre appareil Raspberry Pi, utilisez les commandes suivantes :

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Pour télécharger et installer Node.js v6 sur votre appareil Raspberry Pi, utilisez la commande suivante :

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Pour vérifier que Node.js v6.11.4 a bien été installé, utilisez la commande suivante :

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Créer une solution Node.js

Effectuez les étapes suivantes en utilisant la connexion `ssh` à votre appareil Raspberry Pi :

1. Créez un dossier appelé `remotemonitoring` dans votre dossier de base sur l’appareil Raspberry Pi. Accédez à ce dossier dans votre ligne de commande :

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Pour télécharger et installer les packages dont vous avez besoin pour accomplir l’exemple d’application, exécutez les commandes suivantes :

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Dans le dossier `remotemonitoring`, créez un fichier nommé **remote_monitoring.js**. Ouvrez ce fichier dans un éditeur de texte. Sur l’appareil Raspberry Pi, vous pouvez utiliser l’éditeur de texte `nano` ou `vi`.

1. Dans le fichier **remote_monitoring.js**, ajoutez les instructions `require` suivantes :

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Ajoutez les déclarations de variables suivantes après les instructions `require` . Remplacez la valeur d’espace réservé `{device connection string}` par la valeur que vous avez notée pour l’appareil provisionné dans la solution de supervision à distance :

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Pour définir des données de télémétrie de base, ajoutez les variables suivantes :

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Pour définir des valeurs de propriété, ajoutez les variables suivantes :

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Ajoutez la variable suivante pour définir les propriétés déclarées à envoyer à la solution. Ces propriétés incluent les métadonnées à afficher dans l’interface utilisateur web :

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Pour imprimer les résultats de l’opération, ajoutez la fonction d’assistance suivante :

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Ajoutez la fonction d’assistance suivante qui permet de rendre aléatoires les valeurs de télémétrie :

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Ajoutez la fonction générique suivante pour gérer les appels de méthode directe à partir de la solution. La fonction affiche des informations sur la méthode directe appelée mais, dans cet exemple, ne modifie en aucune façon l’appareil. La solution utilise des méthodes directes pour agir sur les appareils :

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Ajoutez la fonction suivante pour gérer les appels de méthode directe **FirmwareUpdate** à partir de la solution. La fonction vérifie les paramètres passés dans la charge utile de la méthode directe, puis exécute de façon asynchrone une simulation de mise à jour du microprogramme :

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Ajoutez la fonction suivante pour simuler un flux de mise à jour de microprogramme long qui rend compte de la progression à la solution :

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Ajoutez le code suivant pour envoyer les données de télémétrie à la solution. L’application cliente ajoute des propriétés au message pour identifier le schéma du message :

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Ajoutez le code suivant pour créer une instance de client :

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Ajoutez le code suivant à :

    * Ouvrir la connexion.
    * Définir un gestionnaire pour les propriétés souhaitées.
    * Envoyer les propriétés signalées.
    * Inscrire des gestionnaires pour les méthodes directes. L’exemple utilise un gestionnaire distinct pour la méthode directe de mise à jour du microprogramme.
    * Démarrer l’envoi de la télémétrie.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Enregistrez les modifications dans le fichier **remote_monitoring.js**.

1. Pour démarrer l’exemple d’application, exécutez la commande suivante depuis l’invite de commande sur l’appareil Raspberry Pi :

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
