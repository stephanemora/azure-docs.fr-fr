---
title: Ajouter un appareil réel à une application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, ajoutez un appareil réel à votre application Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5933f74dcedb579023d187061229cdd53bce6414
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819431"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Didacticiel : Ajouter un appareil réel à votre application Azure IoT Central

Ce didacticiel vous indique comment ajouter et configurer un appareil réel dans votre application Microsoft Azure IoT Central.

Le didacticiel se déroule en deux parties :

1. En tant qu’opérateur, vous commencez par apprendre à ajouter et configurer un appareil réel dans votre application Azure IoT Central. À la fin de cette partie, vous récupérez une chaîne de connexion à utiliser dans la seconde partie.
2. Puis, en tant que développeur d’appareils, vous vous familiarisez avec le code dans votre appareil réel. Vous ajoutez alors à l’exemple de code la chaîne de connexion récupérée dans la première partie.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un appareil réel
> * Configurer l’appareil réel
> * Obtenir la chaîne de connexion de l’appareil réel à partir de l’application
> * Comprendre la façon dont le code client est mappé sur l’application
> * Configurer le code client pour l’appareil réel

## <a name="prerequisites"></a>Prérequis

Avant de commencer, le générateur doit suivre au minimum le premier des trois didacticiels pour générateur ci-après afin de créer l’application Azure IoT Central :

* [Définir un nouveau type d’appareil](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (obligatoire)
* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (facultatif)
* [Personnaliser la vue de l’opérateur](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (facultatif)

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Pour ajouter un appareil réel à votre application, vous utilisez le modèle d’appareil **Climatiseur connecté** que vous avez créé dans le didacticiel [Définir un nouveau type d’appareil](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Pour ajouter un appareil en tant qu’opérateur, choisissez **Device Explorer** dans le menu de navigation gauche :

   ![Page Device Explorer affichant le modèle de climatiseur connecté](media/tutorial-add-device-experimental/explorer.png)

   L’outil **Device Explorer** affiche le modèle d’appareil **Climatiseur connecté**, ainsi que l’appareil simulé qui a été automatiquement créé lorsque le générateur a créé le modèle d’appareil.

2. Pour démarrer la connexion d’un climatiseur connecté réel, choisissez **Nouveau**, puis **Réel** :

   ![Début de l’ajout d’un nouveau climatiseur connecté réel](media/tutorial-add-device-experimental/newreal.png)

3. Entrez l’ID d’appareil (**doit être en minuscules**) ou utilisez l’ID d’appareil suggéré. Vous pouvez également entrer le nom de votre nouvel appareil et choisir **Créer**.  

   ![Changement du nom de l’appareil](media/tutorial-add-device-experimental/rename.png)

## <a name="configure-a-real-device"></a>Configurer un appareil réel

L’appareil réel est créé à partir du modèle d’appareil **Climatiseur connecté**. Vous pouvez utiliser la page **Paramètres** pour configurer votre appareil et pour définir les valeurs de propriété afin d’enregistrer les informations concernant votre appareil.

1. Sur la page **Settings** (Paramètres), notez que le paramètre **Température réglée** présente l’état **no update** (aucune mise à jour). Il conserve cet état jusqu’à ce que l’appareil réel soit connecté à l’application et qu’il confirme la prise en compte du paramètre. 

    ![Page de paramètres indiquant l’état de synchronisation](media/tutorial-add-device-experimental/settingssyncing.png)

2. Sur la page **Propriétés** pour votre nouvel appareil climatiseur réel, l’emplacement du service et la dernière date de service sont les deux propriétés modifiables de l’appareil. Les champs de la version du microprogramme et du numéro de série sont vides jusqu’à ce que l’appareil soit connecté à l’application. Il s’agit des valeurs en lecture seule envoyées à partir de l’appareil et elles ne peuvent pas être modifiées.

    ![Propriétés de l’appareil pour un appareil réel](media/tutorial-add-device-experimental/setproperties1.png)

3. Vous pouvez visualiser les pages **Mesures**, **Règles** et **Tableau de bord** de votre appareil réel.

## <a name="generate-connection-string-for-real-device-from-application"></a>Générer une chaîne de connexion de l’appareil réel à partir de l’application

Un développeur d’appareils doit incorporer la *chaîne de connexion* de votre appareil réel dans le code qui s’exécute sur l’appareil. La chaîne de connexion permet à l’appareil de se connecter en toute sécurité à votre application Azure IoT Central. Vous générez la chaîne de connexion durant la préparation du code client écrit en Node.js dans les étapes suivantes. L’application Node.js représente le climatiseur connecté réel. 

## <a name="prepare-the-client-code"></a>Préparer le code client

L’exemple de code fourni dans cet article est écrit en [Node.js](https://nodejs.org/) et présente juste assez de code pour :

* se connecter en tant qu’appareil à votre application Azure IoT Central ;
* envoyer les données de télémétrie de température en tant que climatiseur connecté ;
* envoyer des propriétés d’appareil à votre application Azure IoT Central ;
* répondre à un opérateur qui utilise le paramètre **Température réglée**.
* gérer la commande Echo depuis votre application Azure IoT Central.

Les articles de procédure référencés à la section [Étapes suivantes](#next-steps) fournissent des exemples plus complets et illustrent l’utilisation d’autres langages de programmation. Pour plus d’informations sur la connexion d’appareils à Azure IoT Central, consultez l’article [Connectivité des appareils](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

La procédure ci-après vous indique comment préparer l’exemple [Node.js](https://nodejs.org/) :

1. Installez [Node.js](https://nodejs.org/) 4.0.x ou une version ultérieure sur votre machine. Node.js est disponible pour un large éventail de systèmes d’exploitation.

1. Sur votre machine, créez un dossier nommé `connectedairconditioner`.

1. Dans votre environnement de ligne de commande, accédez au dossier `connectedairconditioner` que vous venez de créer.

1. Installez le générateur de clé DPS en exécutant la commande suivante :

    ```cmd/sh
    npm i -g dps-keygen
    ```

   Vous pouvez trouver plus d’informations sur l’outil en ligne de commande [ici](https://www.npmjs.com/package/dps-keygen).

1. La chaîne de connexion pour une instance d’appareil dans votre application est générée à partir des informations sur l’appareil fournies par l’IoT Central.

   Revenez au portail de l’IoT Central. Sur l’écran Appareil de votre climatiseur connecté réel, choisissez **Connecter**.

   ![Page Appareil affichant le lien d’accès aux informations de connexion](media/tutorial-add-device-experimental/connectionlink.png)

1. Dans la page de connexion de l’appareil, copiez et collez l’ID d’étendue, l’ID de l’appareil et la clé primaire dans un éditeur de texte, puis enregistrez. Vous utiliserez ces valeurs à l’étape suivante.

   ![Informations de connexion](media/tutorial-add-device-experimental/device-connect.png)

1. Revenez à l’environnement de ligne de commande et générez votre chaîne de connexion en exécutant :

    ```cmd/sh
    dps_keygen <scope_id> <device_id> <Primary Key>
    ```

   Copiez le résultat et enregistrez-le dans un nouveau fichier (par exemple, connection.txt).

1. Pour initialiser votre projet Node.js, exécutez la commande ci-après en acceptant toutes les valeurs par défaut :

    ```cmd/sh
    npm init
      ```

1. Pour installer les packages nécessaires, exécutez la commande suivante :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. À l’aide d’un éditeur de texte, créez un fichier appelé **ConnectedAirConditioner.js** dans le dossier `connectedairconditioner`.

1. Ajoutez les instructions `require` ci-après au début du fichier **ConnectedAirConditioner.js** :

    ```javascript
    'use strict';

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

    > [!NOTE]
    > Vous mettrez à jour l’espace réservé `{your device connection string}` dans le cadre d’une étape ultérieure.

1. Enregistrez les modifications que vous venez d’effectuer, mais ne fermez pas le fichier.

## <a name="understand-how-client-code-maps-to-the-application"></a>Comprendre la façon dont le code client est mappé sur l’application

À la section précédente, vous avez créé un projet Node.js squelette pour une application qui se connecte à votre application Azure IoT Central. Dans cette section, vous allez ajouter du code pour :

* vous connecter à votre application Azure IoT Central ;
* envoyer des données de télémétrie à votre application Azure IoT Central ;
* envoyer des propriétés d’appareil à votre application Azure IoT Central ;
* recevoir des paramètres de votre application Azure IoT Central.
* gérer la commande Echo depuis votre application Azure IoT Central.

1. Pour envoyer les données de télémétrie de température à votre application Azure IoT Central, ajoutez le code ci-après au fichier **ConnectedAirConditioner.js** :

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Setup device command callbacks
        client.onDeviceMethod('echo', onCommandEcho);
        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. Enregistrez les modifications que vous venez d’effectuer, mais ne fermez pas le fichier.

## <a name="configure-client-code-for-the-real-device"></a>Configurer le code client pour l’appareil réel

<!-- Add the connection string to the sample code, build, and run --> Pour configurer votre code client pour la connexion à votre application Azure IoT Central, vous devez ajouter la chaîne de connexion de votre appareil réel que vous avez notée au cours d’une étape précédente de ce tutoriel.

1. Dans le fichier **ConnectedAirConditioner.js**, recherchez la ligne de code suivante :

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. Remplacez `{your device connection string}` par la chaîne de connexion de votre appareil réel. Vous avez enregistré la chaîne de connexion précédemment dans un éditeur de texte.

1. Enregistrez les modifications apportées au fichier **ConnectedAirConditioner.js**.

1. Pour exécuter l’exemple, entrez la commande ci-après dans votre environnement de ligne de commande :

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Vérifiez que vous exécutez cette commande à partir du dossier `connectedairconditioner`.

1. L’application affiche la sortie sur la console :

   ![Sortie de l’application cliente](media/tutorial-add-device-experimental/output.png)

1. Au bout de 30 secondes environ, vous voyez les données de télémétrie apparaître sur la page **Measurements** (Mesures) de l’appareil :

   ![Télémétrie réelle](media/tutorial-add-device-experimental/realtelemetry.png)

1. Sur la page **Settings** (Paramètres), vous pouvez constater que le paramètre est à présent synchronisé. Lorsque l’appareil s’est connecté pour la première fois, il a reçu la valeur du paramètre et a accusé réception de sa modification :

   ![Paramètre synchronisé](media/tutorial-add-device-experimental/settingsynced.png)

1. Sur la page **Settings** (Paramètres), définissez la température de l’appareil sur **95**, puis choisissez **Update device** (Mettre à jour l’appareil). Votre exemple d’application reçoit et traite cette modification :

   ![Réception et traitement du paramètre](media/tutorial-add-device-experimental/receivesetting.png)

   > [!NOTE]
   > Il existe deux types de messages « setting update » : l’un lorsque l’état `pending` est envoyé, et l’autre lors de l’envoi de l’état `completed`.

1. Sur la page **Measurements** (Mesures), vous pouvez constater que l’appareil envoie des valeurs de température plus élevées :

    ![Données de télémétrie de température plus élevées](media/tutorial-add-device-experimental/highertemperature.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="nextstepaction"]
> * Ajouter un appareil réel
> * Configurer le nouvel appareil
> * Obtenir la chaîne de connexion de l’appareil réel à partir de l’application
> * Comprendre la façon dont le code client est mappé sur l’application
> * Configurer le code client pour l’appareil réel

Maintenant que vous avez connecté un appareil réel à votre application Azure IoT Central, voici les prochaines étapes suggérées :

En tant qu’opérateur, vous pouvez apprendre à :

* [Gestion de vos appareils](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Utiliser des ensembles d’appareils](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Créer une analyse personnalisée](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

En tant que développeur d’appareils, vous pouvez apprendre à :

* [Préparer et connecter un appareil DevKit](howto-connect-devkit-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Préparer et connecter un Raspberry Pi](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Personnaliser votre code][lnk-nodejs-device-ref]


[lnk-nodejs-device-ref]: /javascript/api/azure-iot-device/?view=azure-iot-typescript-latest