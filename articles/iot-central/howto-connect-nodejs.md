---
title: Connecter une application cliente Node.js générique à Azure IoT Central | Microsoft Docs
description: En tant que périphérique développeur, comment connecter un appareil Node.js générique à votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5497e4956fbdc74eced302867c33a66d07d6a184
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617935"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Connecter une application cliente Node.js générique à votre application Azure IoT Central (Node.js)

Cet article vous explique comment, en tant que développeur d’appareils, connecter une application Node.js générique représentant un appareil réel à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
1. Une machine de développement où [Node.js](https://nodejs.org/) version 4.0.0 ou ultérieure est installé. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

Dans votre application Azure IoT Central, vous avez besoin d’un modèle de périphérique avec les mesures, propriétés de l’appareil, paramètres et commandes suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

Ajouter les données de télémétrie suivantes sur le **mesures** page :

| Nom d’affichage | Nom du champ  | Units | Min | max | Nombre de décimales |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Température  | température | F     | 60  | 110 | 0              |
| Humidité     | humidité    | %     | 0   | 100 | 0              |
| Pression     | pression    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> La mesure de télémétrie est représentée par un nombre à virgule flottante.

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champs ne correspondent pas les noms de propriété dans le code d’appareil correspondant, les données de télémétrie ne peut pas être affichée dans l’application.

### <a name="state-measurements"></a>Mesures d’état

Ajouter l’état suivant sur le **mesures** page :

| Nom d’affichage | Nom du champ  | Valeur 1 | Nom d’affichage | Valeur 2 | Nom d’affichage |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Mode du ventilateur     | fanmode     | 1       | Exécution      | 0       | Arrêté      |

> [!NOTE]
> Le type de données de la mesure État est « chaîne ».

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champs ne correspondent pas les noms de propriété dans le code d’appareil correspondant, l’état ne peut pas être affichée dans l’application.

### <a name="event-measurements"></a>Mesures d’événement

Ajoutez l’événement suivant sur le **mesures** page :

| Nom d’affichage | Nom du champ  | Severity |
| ------------ | ----------- | -------- |
| Surchauffe  | overheat    | Error    |

> [!NOTE]
> Le type de données de la mesure Événement est « chaîne ».

### <a name="device-properties"></a>Propriétés de l’appareil

Ajouter les propriétés d’appareil suivantes sur le **propriétés** page :

| Nom d’affichage        | Nom du champ        | Type de données |
| ------------------- | ----------------- | --------- |
| Numéro de série       | serialNumber      | texte      |
| Fabricant de l’appareil | manufacturer      | texte      |

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champs ne correspondent pas les noms de propriété dans le code d’appareil correspondant, les propriétés ne peuvent pas être affichées dans l’application.

### <a name="settings"></a>Paramètres

Ajoutez le code suivant **nombre** paramètres sur le **paramètres** page :

| Nom d’affichage    | Nom du champ     | Units | Décimales | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Vitesse du ventilateur       | fanSpeed       | tr/min   | 0        | 0   | 3000 | 0       |
| Température définie | setTemperature | F     | 0        | 20  | 200  | 80      |

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms de champs ne correspondent pas les noms de propriété dans le code d’appareil correspondant, l’appareil ne peut pas recevoir la valeur du paramètre.

### <a name="commands"></a>Commandes

Ajoutez la commande suivante sur le **commandes** page :

| Nom d’affichage    | Nom du champ     | Délai d’expiration par défaut | Type de données |
| --------------- | -------------- | --------------- | --------- |
| Compte à rebours       | countdown      | 30              | number    |

Ajoutez le champ d’entrée suivant à la commande de compte à rebours :

| Nom d’affichage    | Nom du champ     | Type de données | Valeur |
| --------------- | -------------- | --------- | ----- |
| Nombre à partir de      | countFrom      | number    | 10    |

Entrez les noms de champs exactement comme indiqué dans les tables dans le modèle de périphérique. Si les noms de champs ne correspondent pas les noms de propriété dans le code d’appareil correspondant, l’appareil ne peut pas traiter la commande.

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel pour le modèle d’appareil que vous avez créé dans la section précédente.

Puis suivez les instructions dans le didacticiel « Ajouter un appareil » à [générer une chaîne de connexion pour le périphérique réel](tutorial-add-device.md#generate-connection-string). Vous utilisez cette chaîne de connexion dans la section suivante :

### <a name="create-a-nodejs-application"></a>Création d’une application Node.js

Les étapes suivantes montrent comment créer une application cliente qui implémente l’appareil réel que vous avez ajouté à l’application. Ici, l’application Node.js représente l’appareil réel. 

1. Sur votre machine, créez un dossier nommé `connected-air-conditioner-adv`. Accédez à ce dossier dans votre environnement de ligne de commande.

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

1. Ajoutez les déclarations de variable ci-après au fichier :

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Mettre à jour de l’espace réservé `{your device connection string}` avec la [chaîne de connexion de périphérique](tutorial-add-device.md#generate-connection-string). Dans cet exemple, vous initialisez `targetTemperature` à zéro, vous pouvez utiliser la lecture en cours à partir de l’appareil ou une valeur à partir de la représentation d’appareil.

1. Pour envoyer des données de télémétrie, l’état et mesures de l’événement à votre application Azure IoT Central, ajoutez la fonction suivante au fichier :

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
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
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

1. Ajoutez le code suivant pour gérer une commande de compte à rebours envoyée à partir de l’application IoT Central :

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
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

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

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

* Voir les valeurs de propriété d’appareil envoyées depuis votre appareil dans la page **Propriétés**. La propriété de l’appareil dispose en mosaïque mise à jour lorsque l’appareil se connecte :

    ![Voir les propriétés de l’appareil](media/howto-connect-nodejs/viewproperties.png)

* Définir la température de vitesse et la cible de ventilateur à partir de la **paramètres** page :

    ![Définir la vitesse du ventilateur](media/howto-connect-nodejs/setfanspeed.png)

* Appelez la commande de compte à rebours à partir de la **commandes** page :

    ![Compte à rebours des appels, commande](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un client Node.js générique à votre application Azure IoT Central, l’étape suivante suggérée consiste à apprendre comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
