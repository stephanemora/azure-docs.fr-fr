---
title: Utiliser l’API REST pour gérer des appareils dans Azure IoT Central
description: Comment utiliser l’API REST IoT Central pour contrôler les appareils dans une application
author: dominicbetts
ms.author: dobett
ms.date: 06/01/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 56ee24985da57c6da76638b659e5a07e68d0f516
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110788944"
---
# <a name="how-to-use-the-iot-central-rest-api-to-control-devices"></a>Comment utiliser l’API REST IoT Central pour contrôler les appareils

L’API REST IoT Central vous permet de développer des applications clientes qui s’intègrent à des applications IoT Central. Vous pouvez utiliser l’API REST pour contrôler les appareils dans votre application IoT Central. L’API REST vous permet de :

- Lire la dernière valeur connue de télémétrie d’un appareil.
- Lire les valeurs de propriété d’un appareil.
- Définissez des propriétés accessibles en écriture sur un appareil.
- Appeler des commandes sur un appareil.

Cet article explique comment utiliser l’API `/devices/{device_id}` pour contrôler des appareils individuels. Vous pouvez également utiliser des travaux pour contrôler les appareils en bloc.

Un appareil peut regrouper les propriétés, les données de télémétrie et les commandes qu’il prend en charge dans des _composants_ et _modules_.

Chaque appel d’API REST IoT Central nécessite un en-tête d’autorisation. Pour plus d’informations, consultez [Comment authentifier et autoriser les appels d’API REST d’IoT Central](howto-authorize-rest-api.md).

Pour obtenir la documentation de référence sur l’API REST IoT Central, consultez [Informations de référence sur l’API REST d’Azure IoT Central](/rest/api/iotcentral/).

## <a name="components-and-modules"></a>Composants et modules

Les composants vous permettent de regrouper et réutiliser les fonctionnalités des appareils. Pour en savoir plus sur les composants et les modèles d’appareil, consultez le [Guide de modélisation d’IoT Plug-and-Play](../../iot-pnp/concepts-modeling-guide.md).

Tous les modèles d’appareil n’utilisent pas de composants. La capture d’écran suivante montre le modèle d’appareil pour un [thermostat](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-2.json) simple dans lequel toutes les fonctionnalités sont définies dans une interface unique appelée **Composant par défaut** :

:::image type="content" source="media/howto-control-devices-with-rest-api/thermostat-device.png" alt-text="Capture d’écran montrant un simple appareil à thermostat sans composant.":::

La capture d’écran suivante montre un modèle d’appareil de [contrôleur de température](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json) qui utilise des composants. Le contrôleur de température a deux composants de thermostat et un composant d’informations sur l’appareil :

:::image type="content" source="media/howto-control-devices-with-rest-api/temperature-controller-device.png" alt-text="Capture d’écran montrant un appareil de contrôleur de température avec deux composants de thermostat et un composant d’informations sur l’appareil.":::

Dans IoT Central, un module fait référence à un module IoT Edge exécuté sur un appareil IoT Edge connecté. Un module peut avoir un modèle simple, tel que le thermostat qui n’utilise pas de composants. Un module peut également utiliser des composants pour organiser un ensemble plus complexe de fonctionnalités. La capture d’écran suivante montre un exemple de modèle d’appareil qui utilise des modules. Le périphérique capteur d’environnement a un module appelé `SimulatedTemperatureSensor` et une interface héritée appelée `management` :

:::image type="content" source="media/howto-control-devices-with-rest-api/environmental-sensor-device.png" alt-text="Capture d’écran montrant un appareil de capteur d’environnement avec un module.":::

## <a name="get-a-device-component"></a>Obtenir un composant d’appareil

Utilisez la requête suivante pour récupérer les composants d’un appareil appelé `temperature-controller-01` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components?api-version=1.0
```

La réponse à cette demande se présente comme dans l’exemple suivant. Le tableau `value` contient les détails de chaque composant d’appareil :

```json
{
  "value": [
    {
      "@type": "Component",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type": "Component",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

## <a name="get-a-device-module"></a>Obtenir un module d’appareil

Utilisez la requête suivante pour récupérer une liste de modules s’exécutant sur un appareil IoT Edge connecté, appelé `environmental-sensor-01` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

La réponse à cette demande se présente comme dans l’exemple suivant. Le tableau de modules comprend uniquement des modules personnalisés qui s’exécutent sur l’appareil IoT Edge, pas les modules intégrés `$edgeAgent` et `$edgeHub` :

```json
{
  "value": [
    {
      "@type": [
        "Relationship",
        "EdgeModule"
      ],
      "name": "SimulatedTemperatureSensor",
      "displayName": "SimulatedTemperatureSensor"
    }
  ]
}
```

Utilisez la requête suivante pour récupérer une liste des composants dans un module appelé `SimulatedTemperatureSensor` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

## <a name="read-telemetry"></a>Lire les données de télémétrie

Utilisez la requête suivante pour récupérer la dernière valeur connue de télémétrie à partir d’un appareil qui n’utilise pas de composants. Dans cet exemple, l’appareil est appelé `thermostat-01` et la télémétrie est appelée `temperature` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/telemetry/temperature?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "timestamp": "2021-03-24T12:33:15.223Z",
  "value": 40.10993804456927
}
```

Utilisez la requête suivante pour récupérer la dernière valeur connue de télémétrie à partir d’un appareil qui utilise des composants. Dans cet exemple, l’appareil est appelé `temperature-controller-01`, le composant `thermostat2` et la télémétrie `temperature` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/telemetry/temperature?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "timestamp": "2021-03-24T12:43:44.968Z",
  "value": 70.29168040339141
}
```

Si l’appareil est un appareil IoT Edge, utilisez la requête suivante pour récupérer la dernière valeur connue de télémétrie d’un module. Cet exemple utilise un appareil appelé `environmental-sensor-01` avec un module `SimulatedTemperatureSensor` et une télémétrie `ambient`. Le type de télémétrie `ambient` a des valeurs de température et d’humidité :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/telemetry/ambient?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "timestamp": "2021-03-25T15:44:34.955Z",
  "value": {
    "temperature": 21.18032378129676,
    "humidity": 25
  }
}
```

> [!TIP]
> Pour accéder aux données de télémétrie à partir d’un composant dans un module, utilisez `/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/telemetry/{telemetryName}`.

## <a name="read-properties"></a>Lire les propriétés

Utilisez la requête suivante pour récupérer les valeurs de propriété à partir d’un appareil qui n’utilise pas de composants. Dans cet exemple, l’appareil est appelé `thermostat-01` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

La réponse à cette demande se présente comme dans l’exemple suivant. Elle indique que l’appareil signale une seule valeur de propriété :

```json
{
  "maxTempSinceLastReboot": 93.95907131817654,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T12:47:46.7571438Z"
    }
  }
}
```

Utilisez la requête suivante pour récupérer des valeurs de propriété à partir de tous les composants. Dans cet exemple, l’appareil est appelé `temperature-controller-01` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/properties?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "serialNumber": "Explicabo animi nihil qui facere sit explicabo nisi.",
  "$metadata": {
    "serialNumber": {
      "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
    }
  },
  "thermostat1": {
    "maxTempSinceLastReboot": 79.7290121339184,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "thermostat2": {
    "maxTempSinceLastReboot": 54.214860556320424,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "deviceInformation": {
    "manufacturer": "Eveniet culpa sed sit omnis.",
    "$metadata": {
      "manufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "model": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "swVersion": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "osName": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorArchitecture": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorManufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalStorage": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalMemory": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    },
    "model": "Necessitatibus id ab dolores vel eligendi fuga.",
    "swVersion": "Ut minus ipsum ut omnis est asperiores harum.",
    "osName": "Atque sit omnis eum sapiente eum tenetur est dolor.",
    "processorArchitecture": "Ratione enim dolor iste iure.",
    "processorManufacturer": "Aliquam eligendi sit ipsa.",
    "totalStorage": 36.02825898541592,
    "totalMemory": 55.442695395750505
  }
}
```

Utilisez la requête suivante pour récupérer une valeur de propriété à partir d’un composant individuel. Dans cet exemple, l’appareil est appelé `temperature-controller-01` et le composant est appelé `thermostat2` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "maxTempSinceLastReboot": 24.445128131004935,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T14:03:53.787491Z"
    }
  }
}
```

Si l’appareil est un appareil IoT Edge, utilisez la requête suivante pour récupérer les valeurs de propriété à partir d’un module. Cet exemple utilise un appareil appelé `environmental-sensor-01` avec un module `SimulatedTemperatureSensor` :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "$metadata": {
    "SendData": {
      "desiredValue": true,
      "desiredVersion": 1
    },
    "SendInterval": {
      "desiredValue": 10,
      "desiredVersion": 1
    }
  }
}
```

> [!TIP]
> Pour accéder aux propriétés à partir d’un composant dans un module, utilisez `/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties`.

## <a name="write-properties"></a>Propriétés d’écriture

Certaines propriétés sont accessibles en écriture. Par exemple, dans le modèle de thermostat, la propriété `targetTemperature` est une propriété accessible en écriture.

Utilisez la requête suivante pour écrire une valeur de propriété individuelle sur un appareil qui n’utilise pas de composants. Dans cet exemple, l’appareil est appelé `thermostat-01` :

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

Le corps de votre requête ressemble à l’exemple suivant :

```json
{
  "targetTemperature": 65.5
}
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> Pour mettre à jour toutes les propriétés sur un appareil, utilisez `PUT` à la place de `PATCH`.

Utilisez la requête suivante pour écrire une valeur de propriété individuelle sur un appareil qui utilise des composants. Dans cet exemple, l’appareil est appelé `temperature-controller-01` et le composant est appelé `thermostat2` :

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

Le corps de votre requête ressemble à l’exemple suivant :

```json
{
  "targetTemperature": 65.5
}
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> Pour mettre à jour toutes les propriétés sur un composant, utilisez `PUT` à la place de `PATCH`.

Si l’appareil est un appareil IoT Edge, utilisez la requête suivante pour écrire une valeur de propriété individuelle dans un module. Cet exemple utilise un appareil appelé `environmental-sensor-01`, un module `SimulatedTemperatureSensor` et une propriété `SendInterval` :

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

Le corps de votre requête ressemble à l’exemple suivant :

```json
{
  "SendInterval": 20
}
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "$metadata": {
    "SendInterval": {
      "desiredValue": 20
    }
  }
}
```

> [!TIP]
> Pour mettre à jour toutes les propriétés d’un module, utilisez `PUT` au lieu de `PATCH`.

### <a name="update-module-properties"></a>Mettre à jour les propriétés de module

Si vous utilisez un appareil IoT Edge, utilisez la requête suivante pour récupérer les valeurs de propriété à partir d’un module :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/properties?api-version=1.0
```

Si vous utilisez un appareil IoT Edge, utilisez la requête suivante pour récupérer les valeurs de propriété à partir d’un composant dans un module :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties?api-version=1.0
```

## <a name="call-commands"></a>Appeler des commandes

Vous pouvez utiliser l’API REST pour appeler des commandes d’appareil et récupérer l’historique de l’appareil.

Utilisez la requête suivante pour appeler une commande sur un appareil qui n’utilise pas de composants. Dans cet exemple, l’appareil est appelé `thermostat-01` et la commande `getMaxMinReport` :

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

Le corps de votre requête ressemble à l’exemple suivant :

```json
{
  "since": "2021-03-24T12:55:20.789Z"
}
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "response": {
    "maxTemp": 21.002000799562367,
    "minTemp": 73.09674605264892,
    "avgTemp": 59.54553991653756,
    "startTime": "2022-02-28T15:02:56.789Z",
    "endTime": "2021-05-05T03:50:56.412Z"
  },
  "responseCode": 200
}
```

Pour afficher l’historique de cette commande, utilisez la requête suivante :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "value": [
    {
      "response": {
        "maxTemp": 71.43744908819954,
        "minTemp": 51.29986610160005,
        "avgTemp": 39.577384387771744,
        "startTime": "2021-06-20T00:38:17.620Z",
        "endTime": "2022-01-07T22:30:41.104Z"
      },
      "responseCode": 200
    }
  ]
}
```

Utilisez la requête suivante pour appeler une commande sur un appareil qui utilise des composants. Dans cet exemple, l’appareil est appelé `temperature-controller-01`, le composant `thermostat2` et la commande `getMaxMinReport` :

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

Les formats de charge utile de la demande et de réponse sont les mêmes que pour un appareil qui n’utilise pas de composants.

Pour afficher l’historique de cette commande, utilisez la requête suivante :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

> [!TIP]
> Pour appeler des commandes dans un composant de module, utilisez `/devices/{deviceId}/modules/{moduleName}/components/{componentName}/commands/{commandName}`.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à contrôler les appareils avec l’API REST, l’étape suivante suggérée consiste à [Gérer les applications IoT Central avec l’API REST](/learn/modules/manage-iot-central-apps-with-rest-api/).
