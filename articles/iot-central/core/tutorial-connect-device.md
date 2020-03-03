---
title: Tutoriel – Connecter une application cliente Node.js générique à Azure IoT Central | Microsoft Docs
description: Ce tutoriel explique comment, en tant que développeur d’appareils, connecter un appareil exécutant une application cliente Node.js à votre application Azure IoT Central. Vous créez un modèle d’appareil en important un modèle de capacité d’appareil et vous ajoutez des vues qui vous permettent d’interagir avec un appareil connecté.
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624335"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutoriel : Créer et connecter une application cliente Node.js à votre application Azure IoT Central (Node.js)

Ce tutoriel explique comment, en tant que développeur d’appareils, connecter une application cliente Node.js à votre application Azure IoT Central. L’application Node.js simule le comportement d’un appareil réel. Vous utilisez un exemple de _modèle de capacité d’appareil_ pour permettre à un appareil capteur environnemental de créer un _modèle d’appareil_  dans IoT Central. Vous ajoutez des vues au modèle d’appareil pour pouvoir visualiser les données de télémétrie des appareils, gérer les propriétés des appareils et utiliser des commandes pour contrôler vos appareils.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Importer un modèle de capacité d’appareil pour créer un modèle d’appareil.
> * Ajouter des vues par défaut et personnalisées à un modèle d’appareil.
> * Publier un modèle d’appareil et ajouter un appareil réel à votre application IoT Central.
> * Créer et exécuter le code d’appareil Node.js et veiller à ce qu’il se connecte à votre application IoT Central.
> * Affichez la télémétrie simulée que l’appareil envoie.
> * Utiliser une vue pour gérer les propriétés de l’appareil.
> * Appeler des commandes pour contrôler l’appareil.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

* Une application Azure IoT Central créée avec le modèle **Application personnalisée**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Une machine de développement où [Node.js](https://nodejs.org/) version 10.0.0 ou ultérieure est installé. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation. Les instructions de ce tutoriel supposent que vous exécutez la commande **node** à l’invite de commandes Windows. Vous pouvez utiliser Node.js sur divers systèmes d’exploitation.

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

Créez un dossier nommé `environmental-sensor` sur votre ordinateur local.

Téléchargez le fichier JSON de [modèle de capacité de capteur environnemental](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) et enregistrez-le dans le dossier `environmental-sensor`.

Utilisez un éditeur de texte pour remplacer les deux instances de `{YOUR_COMPANY_NAME_HERE}` par le nom de votre société dans le fichier `EnvironmentalSensorInline.capabilitymodel.json` que vous avez téléchargé.

Dans votre application Azure IoT Central, créez un modèle d’appareil appelé *Capteur environnemental* en important le fichier de modèle de capacité d’appareil `EnvironmentalSensorInline.capabilitymodel.json` :

![Modèle d’appareil avec modèle de capacité d’appareil importé](./media/tutorial-connect-device/device-template.png)

Le modèle de capacité d’appareil comprend deux interfaces : l’interface standard **Informations de l’appareil** et l’interface personnalisée **Capteur environnemental**. L’interface **Capteur environnemental** définit les capacités suivantes :

| Type | Nom d’affichage | Description |
| ---- | ------------ | ----------- |
| Propriété | État de l’appareil     | L’état de l’appareil. Deux états, en ligne/hors connexion, sont disponibles. |
| Propriété | Nom du client    | Le nom du client qui utilise actuellement l’appareil. |
| Propriété | Niveau de luminosité | Le niveau de luminosité de la lampe sur l’appareil. Vous pouvez spécifier 1 (haut), 2 (moyen) ou 3 (bas). |
| Télémétrie | Température | La température actuelle sur l’appareil. |
| Télémétrie | Humidité    | L’humidité actuelle sur l’appareil. |
| Commande | blink          | Faire clignoter la LED pendant un temps donné. |
| Commande | turnon         | Allumer le voyant LED sur l’appareil. |
| Commande | turnoff        | Éteindre le voyant LED sur l’appareil. |
| Commande | rundiagnostics | Cette commande démarre l’exécution d’un diagnostic. |

Pour personnaliser l’affichage de la propriété **État de l’appareil** dans votre application IoT Central, sélectionnez **Personnaliser** dans le modèle d’appareil. Développez l’entrée **État de l’appareil**, entrez _En ligne_ comme **Nom true** et _Hors connexion_ comme **Nom false**. Enregistrez les modifications :

![Personnaliser le modèle d’appareil](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Créer des vues

Les vues vous permettent d’interagir avec les appareils connectés à votre application IoT Central. Par exemple, vous pouvez avoir des vues qui affichent les données de télémétrie, des vues qui affichent les propriétés et des vues qui vous permettent de modifier les propriétés de cloud et inscriptibles. Les vues font partie d’un modèle d’appareil.

Pour ajouter des vues par défaut à votre modèle d’appareil **Capteur environnemental**, accédez à votre modèle d’appareil, sélectionnez **Vues**, puis sélectionnez la vignette **Générer les vues par défaut**. Assurez-vous que **Vue d’ensemble** et **À propos** sont **Activées**, puis sélectionnez **Générer les vues de tableau de bord par défaut**. Vous avez maintenant deux vues par défaut définies dans votre modèle.

L’interface **Capteur environnemental** comprend deux propriétés inscriptibles – **Nom du client** et **Niveau de luminosité**. Pour créer une vue, vous pouvez modifier les propriétés suivantes :

1. Sélectionnez **Vues**, puis sélectionnez la vignette **Modification des données de l’appareil et du cloud**.

1. Entrez _Propriétés_ comme nom de formulaire.

1. Sélectionnez les propriétés **Niveau de luminosité** et **Nom du client**. Sélectionnez ensuite **Ajouter une section**.

1. Enregistrez vos modifications.

![Ajout d’une vue permettant de modifier les propriétés](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publier le modèle

Avant d’ajouter un appareil à votre application IoT Central qui utilise le modèle d’appareil **Capteur environnemental**, vous devez publier le modèle.

Dans le modèle d’appareil, sélectionnez **Publier**. Dans le panneau qui affiche les modifications à publier, sélectionnez **Publier**.

Pour vérifier que le modèle est prêt à l’emploi, accédez à la page **Appareils** de votre application IoT Central. La section **Appareils** affiche la liste des appareils publiés dans l’application :

![Page des modèles publiés sur les appareils](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel au modèle d’appareil que vous avez créé dans la section précédente :

1. Dans la page **Appareils**, sélectionnez le modèle d’appareil **Capteur environnemental**.

1. Sélectionnez **+Nouveau**.

1. Veillez à ce que l’option **Simulé** soit définie sur **Désactivé**. Sélectionnez ensuite **Créer**.

Cliquez sur le nom de l’appareil, puis sélectionnez **Connexion**. Prenez note des informations de connexion de l’appareil dans la page **Connexion de l’appareil** – **Étendue de l’ID**, **ID de l’appareil** et **Clé primaire**. Vous avez besoin de ces valeurs pour créer votre code d’appareil :

![Informations de connexion de l’appareil](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Création d’une application Node.js

Les étapes suivantes montrent comment créer une application cliente Node.js qui implémente l’appareil réel que vous avez ajouté à l’application. L’application Node.js simule le comportement d’un appareil réel.

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

1. Pour envoyer des données de télémétrie à votre application Azure IoT Central, ajoutez la fonction suivante au fichier :

    ```javascript
    // Send device measurements.
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

1. Pour envoyer des propriétés d’appareil à votre application Azure IoT Central, ajoutez la fonction suivante à votre fichier :

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Pour définir et gérer les propriétés inscriptibles auxquelles votre appareil répond, ajoutez le code suivant :

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Ajoutez le code suivant pour gérer les commandes envoyées à partir de l’application IoT Central :

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. Ajoutez le code ci-après pour effectuer la connexion à Azure IoT Central et raccorder les fonctions dans le code client :

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
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

![Exécution de l’application cliente](media/tutorial-connect-device/run-application.png)

En tant qu’opérateur dans votre application Azure IoT Central, vous pouvez effectuer les opérations suivantes :

* Affichez les données de télémétrie envoyées par l’appareil dans la page **Vue d’ensemble** :

    ![Afficher les données de télémétrie](media/tutorial-connect-device/view-telemetry.png)

* Mettez à jour les valeurs des propriétés inscriptibles dans la page **Propriétés** :

    ![Mettre à jour des propriétés](media/tutorial-connect-device/update-properties.png)

    ![Mise à jour des propriétés de l’appareil](media/tutorial-connect-device/update-properties-device.png)

* Appelez les commandes à partir de la page **Commandes** :

    ![Appel de la commande blink](media/tutorial-connect-device/call-command.png)

    ![Appel de la commande blink de l’appareil](media/tutorial-connect-device/call-command-device.png)

* Affichez les propriétés de l’appareil dans la page **À propos** :

    ![Afficher les propriétés](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les modèles de capacité d’appareil et sur la façon de créer vos propres modèles d’appareil, poursuivez avec le guide pratique :

> [!div class="nextstepaction"]
> [Définir un nouveau type d’appareil IoT](./howto-set-up-template.md)
