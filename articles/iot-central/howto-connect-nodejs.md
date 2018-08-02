---
title: Connecter une application cliente Node.js générique à Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, comment connecter un appareil Node.js générique à votre application Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 55ce85702804d99d806220d7f0a4ea0820975f4f
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206035"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Connecter une application cliente Node.js générique à votre application Azure IoT Central (Node.js)

Cet article vous explique comment, en tant que développeur d’appareils, connecter une application Node.js générique représentant un appareil physique à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central. Pour plus d’informations, consultez [Créer votre application Azure IoT Central](howto-create-application.md).
1. Une machine de développement où [Node.js](https://nodejs.org/) version 4.0.0 ou ultérieure est installé. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

Dans votre application Azure IoT Central, vous avez besoin d’un modèle d’appareil où les mesures et les propriétés d’appareil suivantes sont définies :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

Ajoutez la télémétrie suivante dans la page **Mesures** :

| Nom d’affichage | Nom du champ  | Units | Min | max | Nombre de décimales |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Température  | température | F     | 60  | 110 | 0              |
| Humidité     | humidité    | %     | 0   | 100 | 0              |
| Pression     | pression    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  Le type de données de la mesure de télémétrie est « double ».

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champ ne correspondent pas, les données de télémétrie ne peuvent pas être affichées dans l’application.

### <a name="state-measurements"></a>Mesures d’état

Ajoutez l’état suivant dans la page **Mesures** :

| Nom d’affichage | Nom du champ  | Valeur 1 | Nom d’affichage | Valeur 2 | Nom d’affichage |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Mode du ventilateur     | fanmode     | 1       | Exécution      | 0       | Arrêté      |

> [!NOTE]
  Le type de données de la mesure État est « chaîne ».

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champ ne correspondent pas, l’état ne peut pas être affiché dans l’application.

### <a name="event-measurements"></a>Mesures d’événement

Ajoutez l’événement suivant dans la page **Mesures** :

| Nom d’affichage | Nom du champ  | Severity |
| ------------ | ----------- | -------- |
| Surchauffe  | overheat    | Error    |

> [!NOTE]
  Le type de données de la mesure Événement est « chaîne ».

### <a name="device-properties"></a>Propriétés de l’appareil

Ajoutez les propriétés d’appareil suivantes dans la **page Propriétés** :

| Nom d’affichage        | Nom du champ        | Type de données |
| ------------------- | ----------------- | --------- |
| Numéro de série       | serialNumber      | texte      |
| Fabricant de l’appareil | manufacturer      | texte      |

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champ ne correspondent pas, l’application ne peut pas afficher la valeur des propriétés.

### <a name="settings"></a>Paramètres

Ajoutez les paramètres **numériques**  suivants dans la **page Paramètres** :

| Nom d’affichage    | Nom du champ     | Units | Décimales | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Vitesse du ventilateur       | fanSpeed       | tr/min   | 0        | 0   | 3000 | 0       |
| Température définie | setTemperature | F     | 0        | 20  | 200  | 80      |

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champ ne correspondent pas, l’appareil ne peut pas recevoir la valeur du paramètre.

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil que vous créez et notez la chaîne de connexion de l’appareil. Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

### <a name="create-a-nodejs-application"></a>Création d’une application Node.js

Les étapes suivantes montrent comment créer une application cliente qui implémente l’appareil réel que vous avez ajouté à l’application.

1. Créez un dossier nommé `connected-air-conditioner-adv` sur votre machine. Accédez à ce dossier dans votre environnement de ligne de commande.

1. Pour initialiser votre projet Node.js, exécutez les commandes suivantes :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Créez un fichier nommé **connectedAirConditionerAdv.js** dans le dossier `connected-air-conditioner-adv`.

1. Ajoutez les instructions `require` suivantes au début du fichier **connectedAirConditionerAdv.js** :

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Ajoutez les déclarations de variable suivantes au fichier :

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Remplacez l’espace réservé `{your device connection string}` par la chaîne de connexion de votre appareil. Vous avez copié cette valeur depuis la page des détails de connexion quand vous avez ajouté votre appareil réel. Dans cet exemple, nous initialisons `targetTemperature` à zéro ; vous pouvez aussi prendre la valeur actuellement lue sur l’appareil ou une valeur du jumeau d’appareil. 

1. Pour envoyer des mesures de télémétrie, d’état et d’événement à votre application Azure IoT Central, ajoutez la fonction suivante au fichier :

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    1. Pour envoyer des propriétés d’appareil à votre application Azure IoT Central, ajoutez la fonction suivante à votre fichier :

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Pour définir les paramètres auxquels votre appareil répond, ajoutez la définition suivante :

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Pour gérer les paramètres mis à jour à partir de votre application Azure IoT Central, ajoutez le code suivant au fichier :

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
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
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Ajoutez ce qui suit pour établir la connexion à Azure IoT Central et raccorder les fonctions dans le code client :

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Exécuter votre application Node.js

Exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd/sh
node connectedAirConditionerAdv.js
```

En tant qu’opérateur dans votre application Azure IoT Central, pour votre appareil réel, vous pouvez :

* Voir la télémétrie dans la page **Mesures** :

    ![Afficher les données de télémétrie](media/howto-connect-nodejs/viewtelemetry.png)

* Voir les valeurs de propriété d’appareil envoyées depuis votre appareil dans la page **Propriétés**.

    ![Voir les propriétés de l’appareil](media/howto-connect-nodejs/viewproperties.png)

* Définissez la vitesse du ventilateur et la température cible dans la page **Paramètres**.

    ![Définir la vitesse du ventilateur](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un client Node.js générique à votre application Azure IoT Central, voici les étapes suivantes suggérées :
* [Préparer et connecter un Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
