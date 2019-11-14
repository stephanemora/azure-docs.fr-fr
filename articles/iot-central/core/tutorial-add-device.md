---
title: Ajouter un appareil réel à une application Azure IoT Central | Microsoft Docs
description: Découvrez comment ajouter et configurer un appareil réel dans votre application Azure IoT Central. Vous pouvez également en apprendre davantage sur le code de votre appareil réel.
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: fce5be4d059d7b3698f1e76a4ec6ff46c4ce3c0b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824605"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Didacticiel : Ajouter un appareil réel à votre application Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ce tutoriel vous indique comment ajouter et configurer un *appareil réel* dans votre application Microsoft Azure IoT Central. Dans ce tutoriel, vous codez l’appareil réel à l’aide de Node.js, puis exécutez le code sur un ordinateur de bureau. Vous n’avez pas besoin d’un appareil IoT distinct, tel qu’un appareil Raspberry Pi ou MXChip IoT DevKit pour suivre ce tutoriel.

Le didacticiel se déroule en deux parties :

* En tant qu’opérateur, vous commencez par apprendre à ajouter et configurer un appareil réel dans votre application Azure IoT Central. À la fin de cette partie, vous récupérez une chaîne de connexion à utiliser dans la seconde partie.
* Puis, en tant que développeur d’appareils, vous vous familiarisez avec le code dans votre appareil réel. Vous ajoutez alors à l’exemple de code la chaîne de connexion récupérée dans la première partie.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un appareil réel
> * Configurer l’appareil réel
> * Obtenir la chaîne de connexion de l’appareil réel à partir de l’application
> * Comprendre la façon dont le code client est mappé sur l’application
> * Configurer le code client pour l’appareil réel

## <a name="prerequisites"></a>Prérequis

Avant de commencer, le générateur doit suivre au minimum le premier des trois didacticiels pour générateur ci-après afin de créer l’application Azure IoT Central : [Définir un nouveau type d’appareil](tutorial-define-device-type.md) (obligatoire)

Installez [Node.js](https://nodejs.org/) version 8.0.0 ou ultérieure sur votre machine de développement. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Pour ajouter un appareil réel à votre application, vous utilisez le modèle d’appareil **Climatiseur connecté** que vous avez créé dans le didacticiel [Définir un nouveau type d’appareil](tutorial-define-device-type.md).

1. Pour ajouter un appareil en tant qu’opérateur, choisissez **Appareils** dans le volet de gauche :

   ![Page Device Explorer affichant le modèle de climatiseur connecté](media/tutorial-add-device/explorer.png)

   L’outil **Device Explorer** affiche le modèle d’appareil **Climatiseur connecté** et un appareil simulé. Quand vous créez un modèle d’appareil, IoT Central crée automatiquement un appareil simulé.

2. Notez que le modèle d’appareil **Climatiseur connecté** est celui qui est sélectionné dans l’**Explorateur d’appareils**. Pour démarrer la connexion d’un climatiseur réel qui utilise ce modèle, sélectionnez **+** , puis **Réel** :

   ![Début de l’ajout d’un nouveau climatiseur connecté réel](media/tutorial-add-device/newreal.png)

3. Entrez votre propre **ID d’appareil** (doit être en minuscules) ou utilisez la valeur suggérée. Vous pouvez également renseigner **Nom de l’appareil** pour votre nouvel appareil et choisir **Créer**.

   ![Changement du nom de l’appareil](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Configurer un appareil réel

L’appareil réel est créé à partir du modèle d’appareil **Climatiseur connecté**. Vous pouvez utiliser la page **Paramètres** pour configurer votre appareil et pour définir les valeurs de propriété afin d’enregistrer les informations concernant votre appareil.

1. Sur la page **Settings** (Paramètres), notez que le paramètre **Température réglée** présente l’état **no update** (aucune mise à jour). Il conserve cet état jusqu’à ce que l’appareil réel soit connecté à l’application et qu’il confirme la prise en compte du paramètre.

    ![Page de paramètres indiquant l’état de synchronisation](media/tutorial-add-device/settingssyncing.png)

2. Dans la page **Propriétés** de votre nouvel appareil réel, l’emplacement du service et la dernière date de service sont des propriétés modifiables. Les champs de la version du microprogramme et du numéro de série sont vides jusqu’à ce que l’appareil soit connecté à l’application. Ces valeurs en lecture seule sont envoyées à partir de l’appareil et ne peuvent pas être modifiées.

    ![Propriétés de l’appareil pour un appareil réel](media/tutorial-add-device/setproperties1.png)

3. Vous pouvez visualiser les pages **Mesures**, **Règles** et **Tableau de bord** de votre appareil réel.

## <a name="prepare-the-client-code"></a>Préparer le code client

L’exemple de code fourni dans cet article est écrit en [Node.js](https://nodejs.org/) et présente assez de code pour un appareil pour :

* vous connecter à votre application Azure IoT Central ;
* envoyer les données de télémétrie de température en tant que climatiseur connecté ;
* envoyer des propriétés d’appareil à votre application Azure IoT Central ;
* répondre à un opérateur qui utilise le paramètre **Température réglée**.
* gérer la commande Echo depuis votre application Azure IoT Central.

Les articles listés dans la section [Étapes suivantes](#next-steps) fournissent des exemples plus complets et montrent d’autres langages de programmation. Pour plus d’informations sur la connexion d’appareils à Azure IoT Central, consultez l’article [Connectivité des appareils](concepts-connectivity.md).

La procédure ci-après vous indique comment préparer l’exemple [Node.js](https://nodejs.org/) :

### <a name="get-the-device-connection-information"></a>Obtenir les informations de connexion de l’appareil

1. La chaîne de connexion pour une instance d’appareil dans votre application est générée à partir des informations sur l’appareil fournies par l’IoT Central.

   Sur l’écran Appareil de votre climatiseur connecté réel, choisissez **Connecter**.

   ![Page Appareil affichant le lien d’accès aux informations de connexion](media/tutorial-add-device/connectionlink.png)

1. Dans la page **Connexion de l’appareil**, notez les valeurs des champs **ID de l’étendue**, **ID de l’appareil** et **Clé primaire**. Vous utiliserez ces valeurs plus tard dans ce tutoriel.

   ![Informations de connexion](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Préparer le projet Node.js

1. Créez un dossier nommé `connectedairconditioner` sur votre ordinateur de développement.

1. Dans votre environnement de ligne de commande, accédez au dossier `connectedairconditioner` que vous venez de créer.

1. Pour initialiser votre projet Node.js, exécutez la commande ci-après en acceptant toutes les valeurs par défaut :

    ```cmd/sh
    npm init
      ```

1. Pour installer les packages nécessaires, exécutez la commande suivante :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. À l’aide d’un éditeur de texte, créez un fichier appelé **ConnectedAirConditioner.js** dans le dossier `connectedairconditioner`.

1. Ajoutez les instructions `require` ci-après au début du fichier **ConnectedAirConditioner.js** :

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Ajoutez les déclarations de variable suivantes au fichier. Remplacez les espaces réservés `{your Scope ID}`, `{your Device ID}` et `{your Primary Key}` par les informations de connexion d’appareil que vous avez notées précédemment :

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Enregistrez les modifications que vous venez d’effectuer, mais ne fermez pas le fichier.

## <a name="review-client-code"></a>Passer en revue le code client

À la section précédente, vous avez créé un projet Node.js squelette pour une application d’appareil qui se connecte à votre application Azure IoT Central. L’étape suivante consiste à ajouter du code pour :

* vous connecter à votre application Azure IoT Central ;
* envoyer des données de télémétrie à votre application Azure IoT Central ;
* envoyer des propriétés d’appareil à votre application Azure IoT Central ;
* recevoir des paramètres de votre application Azure IoT Central.
* gérer la commande Echo depuis votre application Azure IoT Central.

1. Pour envoyer les données de télémétrie de température à votre application Azure IoT Central, ajoutez le code ci-après au fichier **ConnectedAirConditioner.js** :

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Le nom du champ dans le code JSON que vous envoyez doit correspondre à celui que vous avez spécifié pour les données de télémétrie de température dans votre modèle d’appareil. Dans cet exemple, le nom du champ est **temperature**.

1. Pour envoyer les propriétés de l’appareil comme **firmwareVersion** et **serialNumber**, ajoutez la définition suivante :

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Pour définir les paramètres pris en charge par votre appareil, tels que **setTemperature**, ajoutez la définition suivante :

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Pour gérer les paramètres envoyés par Azure IoT Central, ajoutez la fonction ci-après qui localise et exécute le code d’appareil approprié :

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

    Cette fonction :

    * surveille l’envoi par Azure IoT Central d’une propriété requise ;
    * localise la fonction à appeler pour gérer la modification du paramètre ;
    * renvoie un accusé de réception à votre application Azure IoT Central.

1. Pour répondre à une commande telle que **echo** à partir de votre application Azure IoT Central, ajoutez la définition suivante :

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
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
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
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
    ```

1. Inscrivez et connectez l’appareil à votre application IoT Central :

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Enregistrez les modifications que vous avez apportées.

## <a name="run-the-client-code"></a>Exécuter le code client

Vous pouvez maintenant exécuter le code client et observer comment il interagit avec votre application IoT Central :

1. Pour exécuter l’exemple, entrez la commande ci-après dans votre environnement de ligne de commande :

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Vérifiez que vous exécutez cette commande à partir du dossier `connectedairconditioner`.

1. L’application affiche la sortie sur la console :

   ![Sortie de l’application cliente](media/tutorial-add-device/output.png)

1. Au bout de 30 secondes environ, vous voyez les données de télémétrie apparaître sur la page **Measurements** (Mesures) de l’appareil :

   ![Télémétrie réelle](media/tutorial-add-device/realtelemetry.png)

1. Sur la page **Settings** (Paramètres), vous pouvez constater que le paramètre est à présent synchronisé. Lorsque l’appareil s’est connecté pour la première fois, il a reçu la valeur du paramètre et a accusé réception de sa modification :

   ![Paramètre synchronisé](media/tutorial-add-device/settingsynced.png)

1. Sur la page **Settings** (Paramètres), définissez la température de l’appareil sur **95**, puis choisissez **Update device** (Mettre à jour l’appareil). Votre exemple d’application reçoit et traite cette modification :

   ![Réception et traitement du paramètre](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Il existe deux types de messages « setting update » : l’un lorsque l’état `pending` est envoyé, et l’autre lors de l’envoi de l’état `completed`.

1. Sur la page **Measurements** (Mesures), vous pouvez constater que l’appareil envoie des valeurs de température plus élevées :

    ![Données de télémétrie de température plus élevées](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="nextstepaction"]
> * Ajouter un appareil réel
> * Configurer le nouvel appareil
> * Obtenir la chaîne de connexion de l’appareil réel à partir de l’application
> * Comprendre la façon dont le code client est mappé sur l’application
> * Configurer le code client pour l’appareil réel

Maintenant que vous avez connecté un appareil réel à votre application Azure IoT Central, voici certaines prochaines étapes suggérées.

En tant qu’opérateur, vous pouvez apprendre à :

* [Gestion de vos appareils](howto-manage-devices.md)
* [Utiliser des ensembles d’appareils](howto-use-device-sets.md)
* [Créer une analyse personnalisée](howto-use-device-sets.md)

En tant que développeur d’appareils, vous pouvez apprendre à :

* [Préparer et connecter un appareil DevKit (C)](howto-connect-devkit.md)
* [Préparer et connecter un Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Préparer et connecter un Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Préparer et connecter un appareil Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)
