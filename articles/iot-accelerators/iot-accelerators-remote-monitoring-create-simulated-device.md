---
title: Simulation d’appareil avec surveillance à distance IoT - Azure | Microsoft Docs
description: Ce guide pratique montre comment utiliser le simulateur d’appareil avec l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 8babacfede6e13fde629492e1cd9f80af7f0e53f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943068"
---
# <a name="create-and-test-a-new-simulated-device"></a>Créer et tester un appareil simulé

L’accélérateur de solution de supervision à distance vous permet de définir vos propres appareils simulés. Cet article montre comment définir un nouvel appareil Ampoule simulé, et le tester localement. L’accélérateur de solution inclut des appareils simulés tels que des réfrigérateurs et des camions. Vous pouvez cependant définir vos propres appareils simulés pour tester vos solutions IoT avant de déployer des appareils réels.

> [!NOTE]
> Cet article décrit comment utiliser des appareils simulés hébergés dans le service de simulation d’appareil. Si vous voulez créer un appareil réel, consultez [Connecter votre appareil à l’accélérateur de solution de supervision à distance](iot-accelerators-connecting-devices.md).

Ce guide montre comment personnaliser le microservice de simulation d’appareil. Ce microservice fait partie de l’accélérateur de solution de supervision à distance. Pour illustrer les capacités de simulation d’appareil, ce guide pratique utilise deux scénarios dans l’application IoT Contoso :

Dans le premier scénario, vous ajoutez un nouveau type de données de télémétrie au type d’appareil **Chiller** (Refroidisseur) existant de Contoso.

Dans le deuxième scénario, Contoso souhaite tester un nouvel appareil d’éclairage connecté. Pour exécuter les tests, vous créez un appareil simulé ayant les caractéristiques suivantes :

*Propriétés*

| Name                     | Valeurs                      |
| ------------------------ | --------------------------- |
| Couleur                    | White, Red, Blue (Blanc, Rouge, Bleu)            |
| Brightness (Luminosité)               | 0 à 100                    |
| Estimated remaining life (Durée de vie restante estimée) | Compte à rebours à partir de 10 000 heures |

*Télémétrie*

Le tableau suivant présente les données que l’appareil d’éclairage communique au cloud sous la forme d’un flux de données :

| Name   | Valeurs      |
| ------ | ----------- |
| Statut | « on » (allumé), « off » (éteint) |
| Température | Degrés F |
| online | true, false |

> [!NOTE]
> La valeur de télémétrie **online** est obligatoire pour tous les types simulés.

*Méthodes*

Le tableau suivant indique les actions prises en charge par le nouvel appareil :

| Name        |
| ----------- |
| Switch on (Allumer)   |
| Switch off (Éteindre)  |

*État initial*

Le tableau suivant présente l’état initial de l’appareil :

| Name                     | Valeurs |
| ------------------------ | -------|
| Initial color (Couleur initiale)            | White  |
| Initial brightness (Luminosité initiale)       | 75     |
| Initial remaining life (Durée de vie restante initiale)   | 10 000 |
| Initial telemetry status (État initial de la télémétrie) | « on » (allumé)   |
| Initial telemetry temperature (Température initiale de la télémétrie) | 200   |

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les procédures de ce guide, vous avez besoin des éléments suivants :

* Visual Studio Code. Vous pouvez [télécharger Visual Studio Code pour Mac, Linux ou Windows](https://code.visualstudio.com/download).
* .NET Core. Vous pouvez télécharger [.NET Core pour Mac, Linux ou Windows](https://www.microsoft.com/net/download).
* [C# pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postman. Vous pouvez télécharger [Postman pour Mac, Windows ou Linux](https://www.getpostman.com/apps).
* Un [hub IoT déployé dans votre abonnement Azure](../../articles/iot-hub/iot-hub-create-through-portal.md). Vous avez besoin de la chaîne de connexion du hub IoT pour effectuer les étapes décrites dans ce guide. Vous pouvez obtenir cette chaîne de connexion sur le portail Azure.
* Une base de données Cosmos DB qui utilise l’API SQL et qui est configurée avec une [cohérence forte](../../articles/cosmos-db/how-to-manage-database-account.md). Vous avez besoin de la chaîne de connexion de la base de données Cosmos DB pour effectuer les étapes décrites dans ce guide. Vous pouvez obtenir cette chaîne de connexion sur le portail Azure.

## <a name="prepare-your-development-environment"></a>Préparer votre environnement de développement

Effectuez les tâches suivantes pour préparer votre environnement de développement :

* Téléchargez la source du microservice de simulation d’appareil.
* Téléchargez la source du microservice d’adaptateur de stockage.
* Exécutez le microservice d’adaptateur de stockage localement.

Les instructions figurant dans cet article supposent que vous utilisez Windows. Si vous utilisez un autre système d’exploitation, vous devrez changer certains chemins de fichiers et certaines commandes en fonction de votre environnement.

### <a name="download-the-microservices"></a>Télécharger les microservices

Téléchargez et décompressez les [microservices de surveillance à distance](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) à partir de GitHub à un emplacement approprié sur votre machine locale. Dans cet article, nous partons du principe que ce dossier est intitulé **remote-monitoring-services-dotnet-master**.

Téléchargez et décompressez le [microservice de simulation d’appareil](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) à partir de GitHub à un emplacement approprié sur votre machine locale. Dans cet article, nous partons du principe que ce dossier est intitulé **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Exécuter le microservice d’adaptateur de stockage

Ouvrez le dossier **remote-monitoring-services-dotnet-master\storage-adapter** dans Visual Studio Code. Cliquez sur les boutons **Restaurer** pour corriger les éventuelles dépendances non résolues.

Ouvrez le fichier **storage-adapter/WebService/appsettings.ini** et attribuez votre chaîne de connexion Cosmos DB à la variable **documentDBConnectionString**.

Pour exécuter le microservice localement, cliquez sur **Déboguer > Démarrer le débogage**.

La fenêtre **Terminal** dans Visual Studio Code affiche la sortie du microservice en cours d’exécution, y compris une URL pour la vérification d’intégrité du service web : [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). Quand vous accédez à cette adresse, vous devez voir l’état « OK: Alive and well ».

Laissez le microservice d’adaptateur de stockage s’exécuter dans cette instance de Visual Studio Code pendant que vous effectuez les étapes suivantes.

## <a name="modify-the-chiller"></a>Modifier le type Chiller (Refroidisseur)

Dans cette section, vous ajoutez un nouveau type de données de télémétrie, **Internal Temperature** (Température interne), au type d’appareil **Chiller** (Refroidisseur) existant :

1. Créez un dossier **C:\temp\devicemodels** sur votre machine locale.

1. Copiez les fichiers suivants dans votre nouveau dossier à partir de la copie du microservice de simulation d’appareil que vous avez téléchargée :

    | Source | Destination |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Ouvrez le fichier **C:\temp\devicemodels\chiller-01.json**.

1. Dans la section **InitialState**, ajoutez les deux définitions suivantes :

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Dans le tableau **Telemetry**, ajoutez la définition suivante :

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Enregistrez le fichier **C:\temp\devicemodels\chiller-01.json**.

1. Ouvrez le fichier **C:\temp\devicemodels\scripts\chiller-01-state.js**.

1. Ajoutez les champs suivants à la variable **state** :

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Mettez à jour la fonction **main** comme suit :

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Enregistrez le fichier **C:\temp\devicemodels\scripts\chiller-01-state.js**.

## <a name="create-the-lightbulb"></a>Créer l’appareil d’éclairage

Dans cette section, vous définissez un nouveau type d’appareil **Lightbulb** (Appareil d’éclairage) :

1. Créez un fichier **C:\temp\devicemodels\lightbulb-01.json** et ajoutez-y le contenu ci-dessous :

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Enregistrez les modifications dans **C:\temp\devicemodels\lightbulb-01.json**.

1. Créez un fichier **C:\temp\devicemodels\scripts\lightbulb-01-state.js** et ajoutez-y le contenu ci-dessous :

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Enregistrez les modifications dans **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Créez un fichier **C:\temp\devicemodels\scripts\SwitchOn-method.js** et ajoutez-y le contenu ci-dessous :

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Enregistrez les modifications dans **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Créez un fichier **C:\temp\devicemodels\scripts\SwitchOff-method.js** et ajoutez-y le contenu ci-dessous :

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Enregistrez les modifications dans **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Vous venez de créer une version personnalisée du type d’appareil **Chiller** et d’ajouter un nouveau type d’appareil **Lightbulb**.

## <a name="test-the-devices"></a>Tester les appareils

Dans cette section, vous testez les types d’appareils que vous avez créés localement aux sections précédentes.

### <a name="run-the-device-simulation-microservice"></a>Exécuter le microservice de simulation d’appareil

Ouvrez le dossier **device-simulation-dotnet-master** que vous avez téléchargé à partir de GitHub dans une nouvelle instance de Visual Studio Code. Cliquez sur les boutons **Restaurer** pour corriger les éventuelles dépendances non résolues.

Ouvrez le fichier **WebService/appsettings.ini**, attribuez votre chaîne de connexion Cosmos DB à la variable **documentdb_connstring** et modifiez les paramètres comme suit :

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Pour exécuter le microservice localement, cliquez sur **Déboguer > Démarrer le débogage**.

La fenêtre **Terminal** dans Visual Studio Code affiche la sortie du microservice en cours d’exécution.

Laissez le microservice de simulation d’appareil s’exécuter dans cette instance de Visual Studio Code pendant que vous effectuez les étapes suivantes.

### <a name="set-up-a-monitor-for-device-events"></a>Configurer un moniteur des événements des appareils

Dans cette section, vous utilisez l’interface Azure CLI pour configurer un moniteur d’événements qui affiche les données de télémétrie envoyées par les appareils connectés à votre hub IoT.

Le script suivant suppose que le nom de votre hub IoT est **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Laissez le moniteur d’événements s’exécuter pendant que vous testez les appareils simulés.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Créer une simulation avec le type d’appareil Chiller mis à jour

Dans cette section, vous utilisez l’outil Postman pour demander au microservice de simulation d’appareil de démarrer une simulation en utilisant le type d’appareil Chiller mis à jour. Postman est un outil qui permet d’envoyer des demandes REST à un service web. Les fichiers de configuration de Postman dont vous avez besoin se trouvent dans votre copie locale du dépôt **device-simulation-dotnet**.

Pour configurer Postman :

1. Ouvrez Postman sur votre machine locale.

1. Cliquez sur **File > Import** (Fichier > Importer). Cliquez ensuite sur **Choose Files** (Choisir des fichiers).

1. Accédez au dossier **device-simulation-dotnet-master/docs/postman**. Sélectionnez **Azure IoT Device Simulation solution accelerator.postman_collection** et **Azure IoT Device Simulation solution accelerator.postman_environment**, puis cliquez sur **Open** (Ouvrir).

1. Développez **Azure IoT Device Simulation solution accelerator** (Accélérateur de solution Simulation d’appareil Azure IoT) pour afficher les demandes que vous pouvez envoyer.

1. Cliquez sur **No Environment** (Aucun environnement) et sélectionnez **Azure IoT Device Simulation solution accelerator** (Accélérateur de solution Simulation d’appareil Azure IoT).

Vous avez maintenant chargé une collection et un environnement dans votre espace de travail Postman que vous pouvez utiliser pour interagir avec le microservice de simulation d’appareil.

Pour configurer et démarrer la simulation :

1. Dans la collection Postman, sélectionnez **Create modified chiller simulation** (Créer une simulation Chiller modifiée) et cliquez sur **Send** (Envoyer). Cette demande crée quatre instances du type d’appareil Chiller simulé.

1. La sortie du moniteur d’événements dans la fenêtre Azure CLI affiche les données de télémétrie provenant des appareils simulés, y compris les nouvelles valeurs **internal_temperature**.

Pour arrêter la simulation, sélectionnez la demande **Stop simulation** (Arrêter la simulation) dans Postman, puis cliquez sur **Send** (Envoyer).

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Créer une simulation avec le type d’appareil Lightbulb (Appareil d’éclairage)

Dans cette section, vous utilisez l’outil Postman pour demander au microservice de simulation d’appareil de démarrer une simulation en utilisant le type d’appareil Lightbulb. Postman est un outil qui permet d’envoyer des demandes REST à un service web.

Pour configurer et démarrer la simulation :

1. Dans la collection Postman, sélectionnez **Create lightbulb simulation** (Créer une simulation Lightbulb) et cliquez sur **Send** (Envoyer). Cette demande crée deux instances du type d’appareil Lightbulb simulé.

1. La sortie du moniteur d’événements dans la fenêtre Azure CLI affiche les données de télémétrie provenant des appareils Lightbulb simulés.

Pour arrêter la simulation, sélectionnez la demande **Stop simulation** (Arrêter la simulation) dans Postman, puis cliquez sur **Send** (Envoyer).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez arrêter les deux microservices exécutés localement dans leurs instances de Visual Studio Code (**Déboguer > Arrêter le débogage**).

Si vous n’avez plus besoin des instances IoT Hub et Cosmos DB, supprimez-les de votre abonnement Azure pour éviter toute facturation inutile.

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment créer des types d’appareils simulés personnalisés, et les tester en exécutant le microservice de simulation d’appareil localement.

L’étape suivante suggérée est d’apprendre à déployer vos types d’appareils simulés personnalisés sur l’[accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
