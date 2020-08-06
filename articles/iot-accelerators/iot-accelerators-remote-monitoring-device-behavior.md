---
title: Appareil simulé dans la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article explique comment utiliser JavaScript pour définir le comportement d’un appareil simulé dans la solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: 4840363b2f6645fceca80f6dd57ece64fb2c8163
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419918"
---
# <a name="implement-the-device-model-behavior"></a>Implémenter le comportement de modèle d’appareil

L’article [Comprendre le schéma de modèle d’appareil](iot-accelerators-remote-monitoring-device-schema.md) décrit le schéma qui définit un modèle d’appareil simulé. Cet article s’appuie sur deux types de fichiers JavaScript qui implémentent le comportement d’un appareil simulé :

- Les fichiers JavaScript d’**état**, qui s’exécutent à intervalle régulier pour mettre à jour l’état interne de l’appareil.
- Les fichiers JavaScript de **méthode**, qui s’exécutent lorsque la solution appelle une méthode sur l’appareil.

> [!NOTE]
> Les comportements de modèle d’appareil sont destinés uniquement aux appareils simulés hébergés dans le service de simulation d’appareil. Si vous voulez créer un appareil réel, consultez [Connecter votre appareil à l’accélérateur de solution de supervision à distance](iot-accelerators-connecting-devices.md).

Dans cet article, vous apprendrez comment :

>[!div class="checklist"]
> * Contrôler l’état d’un appareil simulé
> * Définir comment un appareil simulé répond à un appel de méthode à partir de la solution de supervision à distance
> * Déboguer vos scripts

## <a name="state-behavior"></a>Comportement d’état

La section [Simulation](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) du schéma du modèle d’appareil définit l’état interne d’un appareil simulé :

- `InitialState` définit les valeurs initiales de toutes les propriétés de l’objet état de l’appareil.
- `Script` identifie un fichier JavaScript qui s’exécute selon une planification pour mettre à jour l’état de l’appareil.

L’exemple suivant montre la définition de l’objet état de l’appareil pour un appareil de refroidissement simulé :

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

L’état de l’appareil simulé, tel que défini dans la section `InitialState`, est conservé en mémoire par le service de simulation. Les informations d’état sont transmises comme entrées à la fonction `main` définie dans **chiller-01-state.js**. Dans cet exemple, le service de simulation exécute le fichier **chiller-01-state.js** toutes les cinq secondes. Le script peut modifier l’état de l’appareil simulé.

L’exemple suivant montre le plan d’une fonction `main` type :

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Le paramètre `context` possède les propriétés suivantes :

- `currentTime` est une chaîne au format `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, par exemple `Simulated.Chiller.123`
- `deviceModel`, par exemple `Chiller`

Le paramètre `state` inclut l’état de l’appareil tel que géré par le service de simulation d’appareil. Cette valeur est l’objet `state` renvoyé par l’appel précédent à `main`.

L’exemple suivant illustre une implémentation standard de la méthode `main` pour gérer l’état du périphérique conservé par le service de simulation :

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

L’exemple suivant montre comment la méthode `main` peut simuler les valeurs de télémétrie qui varient au fil du temps :

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Vous pouvez afficher le fichier [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) complet sur GitHub.

## <a name="method-behavior"></a>Comportement de la méthode

La section [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) du schéma du modèle d’appareil définit les méthodes auxquelles un appareil simulé répond.

L’exemple suivant répertorie la liste des méthodes prises en charge par un appareil de refroidissement simulé :

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Chaque méthode est associée à un fichier JavaScript qui implémente le comportement de la méthode.

L’état de l’appareil simulé, tel que défini dans la section `InitialState` du schéma, est conservé en mémoire par le service de simulation. Les informations d’état sont transmises comme entrées à la fonction `main` définie dans le fichier JavaScript lorsque la méthode est appelée. Le script peut modifier l’état de l’appareil simulé.

L’exemple suivant montre le plan d’une fonction `main` type :

```javascript
function main(context, previousState, previousProperties) {

}
```

Le paramètre `context` possède les propriétés suivantes :

- `currentTime` est une chaîne au format `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, par exemple `Simulated.Chiller.123`
- `deviceModel`, par exemple `Chiller`

Le paramètre `state` inclut l’état de l’appareil tel que géré par le service de simulation d’appareil.

Le paramètre `properties` contient les propriétés de l’appareil qui sont écrites comme propriétés signalées dans le jumeau d’appareil IoT Hub.

Il existe trois fonctions globales que vous pouvez utiliser pour aider à implémenter le comportement de la méthode :

- `updateState` pour mettre à jour l’état conservé par le service de simulation.
- `updateProperty` pour mettre à jour une propriété d’appareil unique.
- `sleep` pour suspendre l’exécution pour simuler une tâche de longue durée.

L’exemple suivant montre une version abrégée du script **IncreasePressure-method.js** utilisé par les appareils de refroidissement simulés :

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Débogage des fichiers de script

Il n’est pas possible de joindre un débogueur à l’interpréteur Javascript utilisé par le service de simulation d’appareil pour exécuter des scripts d’état et de méthode. Toutefois, vous pouvez enregistrer les informations dans le journal du service. La fonction `log()` intégrée vous permet d’enregistrer les informations pour suivre et déboguer l’exécution d’une fonction.

En cas d’erreur de syntaxe, l’interpréteur échoue et écrit une entrée `Jint.Runtime.JavaScriptException` dans le journal du service.

L’article [Exécution du service en local](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) sur GitHub vous explique comment exécuter le service de simulation d’appareil en local. L’exécution du service en local facilite le débogage de vos appareils simulés avant de les déployer dans le cloud.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a décrit comment définir le comportement de votre propre modèle personnalisé d’appareil simulé. Cet article vous a montré comment :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Contrôler l’état d’un appareil simulé
> * Définir comment un appareil simulé répond à un appel de méthode à partir de la solution de supervision à distance
> * Déboguer vos scripts

Maintenant que vous avez découvert comment spécifier le comportement d’un appareil simulé, nous vous recommandons d’apprendre à [Créer un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md).

Pour plus d’informations sur le développement de la solution de supervision à distance, consultez :

* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guide de résolution des problèmes pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
