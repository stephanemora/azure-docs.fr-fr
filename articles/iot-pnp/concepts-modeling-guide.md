---
title: Comprendre les modèles d’appareils IoT Plug-and-Play | Microsoft Docs
description: Comprenez le langage de modélisation DTDL (Digital Twins Definition Language) pour les appareils IoT Plug-and-Play. L’article décrit les types de données primitifs et complexes, réutilise des modèles qui utilisent des composants et l’héritage, ainsi que les types de sémantiques. L’article fournit des conseils sur le choix de l’identificateur de modèle de jumeau d’appareil et la prise en charge des outils pour la création de modèles.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 85888370106f34c723be4e47738114f7df33dcf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057471"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Guide de modélisation d’IoT Plug-and-Play

Au cœur d’IoT Plug-and-Play se trouve un _modèle_ d’appareil qui décrit les capacités d’un appareil à une application IoT Plug-and-Play. Ce modèle est structuré sous la forme d’un ensemble d’interfaces qui définissent ce qui suit :

- _Propriétés_ qui représentent l’état en lecture seule ou en écriture d’un appareil ou d’une autre entité. Par exemple, un numéro de série d’appareil peut être une propriété en lecture seule et une température cible sur un thermostat peut être une propriété en écriture.
- Les champs _Télémétrie_ définissent les données émises par un appareil, qu’il s’agisse d’un flux régulier de relevés de capteurs, d’une erreur occasionnelle ou d’un message d’information.
- _Commandes_ qui décrivent une fonction ou une opération qui peut être effectuée sur un appareil. Par exemple, une commande peut redémarrer une passerelle ou prendre une photo à l’aide d’une caméra distante.

Pour en savoir plus sur la façon dont IoT Plug-and-Play utilise les modèles d’appareils, consultez le [Guide du développeur de l'appareil IoT Plug-and-Play](concepts-developer-guide-device.md) et le [Guide du développeur du service IoT Plug-and-Play](concepts-developer-guide-service.md).

Pour définir un modèle, vous utilisez le langage DTDL (Digital Twins Definition Language). DTDL utilise une variante JSON nommée [JSON-LD](https://json-ld.org/). L’extrait suivant montre le modèle pour un appareil à thermostat qui :

- A un ID de modèle unique : `dtmi:com:example:Thermostat;1`.
- Envoie des données de télémétrie de température.
- A une propriété accessible en écriture pour définir la température cible.
- A une propriété en lecture seule pour indiquer la température maximale depuis le dernier redémarrage.
- Répond à une commande qui demande des températures maximale, minimale et moyenne sur une période.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Le modèle de thermostat a une interface unique. Les exemples ultérieurs dans cet article montrent des modèles plus complexes qui utilisent des composants et l’héritage.

Cet article explique comment concevoir et créer vos propres modèles et couvre des sujets tels que les types de données, la structure du modèle et les outils.

Pour en savoir plus, consultez la spécification [Digital Twins Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="model-structure"></a>Structure du modèle

Les propriétés, les données de télémétrie et les commandes sont regroupées dans des interfaces. Cette section décrit comment vous pouvez utiliser des interfaces pour décrire des modèles simples et complexes à l’aide de composants et de l’héritage.

### <a name="model-ids"></a>ID de modèle

Chaque interface possède un identificateur de modèle de jumeau numérique (DTMI) unique. Les modèles complexes utilisent des DTMI pour identifier des composants. Les applications peuvent utiliser les DTMI que les appareils envoient pour localiser des définitions de modèle dans un référentiel.

Les DTMI doivent respecter la convention d’affectation de noms requise par le [référentiel de modèles IoT Plug-and-Play](https://github.com/Azure/iot-plugandplay-models) :

- Le préfixe DTMI est `dtmi:`.
- Le suffixe DTMI est le numéro de version du modèle, par exemple `;2`.
- Le corps du DTMI est mappé au dossier et au fichier dans le référentiel de modèles où le modèle est stocké. Le numéro de version fait partie du nom de fichier.

Par exemple, le modèle identifié par le DTMI `dtmi:com:Example:Thermostat;2` est stocké dans le fichier *dtmi/com/example/thermostat-2.json*.

L’extrait de code suivant montre le plan d’une définition d’interface avec son DTMI unique :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Aucun composant

Un modèle simple, tel que le thermostat présenté précédemment, n’utilise pas de composants incorporés ou en cascade. Les données de télémétrie, les propriétés et les commandes sont définies dans le nœud `contents` de l’interface.

L’exemple suivant montre une partie d’un modèle simple qui n’utilise pas de composants :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Des outils, tels que l’Explorateur Azure IoT et le concepteur de modèles d’appareils IoT Central étiquettent une interface autonome comme le thermostat en tant que _composant par défaut_.

La capture d’écran suivante montre la façon dont le modèle s’affiche dans l’outil Explorateur Azure IoT :

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Composant par défaut dans l’explorateur Azure IoT":::

La capture d’écran suivante montre comment le modèle s’affiche en tant que composant par défaut dans le concepteur de modèles d’appareils IoT Central. Sélectionnez **Afficher l’identité** pour afficher le DTMI du modèle :

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Capture d’écran montrant le modèle de thermostat dans le concepteur IoT Central":::

L’ID de modèle est stocké dans une propriété de jumeau d’appareil, comme le montre la capture d’écran suivante :

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="ID de modèle dans la propriété du jumeau numérique":::

Un modèle DTDL sans composants est une simplification utile pour un appareil ou un module IoT Edge doté d’un ensemble de données de télémétrie, de propriétés et de commandes unique. Un modèle qui n’utilise pas de composants facilite la migration d’un appareil ou module existant en appareil IoT Plug-and-Play : vous créez un modèle DTDL qui décrit votre appareil ou module réel sans avoir à définir de composants.

> [!TIP]
> Un module peut être un [module](../iot-hub/iot-hub-devguide-module-twins.md) d’appareil ou un [module IoT Edge](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Réutiliser

Deux méthodes permettent de réutiliser les définitions d’interface. Utilisez plusieurs composants dans un modèle pour référencer d’autres définitions d’interface. Utilisez l’héritage pour étendre les définitions d’interface existantes.

### <a name="multiple-components"></a>Plusieurs composants

Les composants vous permettent de créer une interface de modèle qui représente un assemblage d’autres interfaces.

Par exemple, l’interface [Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) est définie comme modèle. Vous pouvez incorporer cette interface comme un ou plusieurs composants lorsque vous définissez le [modèle Contrôleur de température](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). Dans l’exemple suivant, ces composants sont appelés `thermostat1` et `thermostat2`.

Pour un modèle DTDL avec plusieurs composants, il y a deux ou plusieurs sections de composants. Chaque section a `@type` défini sur `Component` et fait explicitement référence à un schéma, comme indiqué dans l’extrait de code suivant :

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Ce modèle a trois composants définis dans la section de contenu : deux composants `Thermostat` et un composant `DeviceInformation`. La section de contenu comprend également les définitions de propriété, de télémétrie et de commande.

Les captures d’écran suivantes montrent comment ce modèle apparaît dans IoT Central. Les définitions de propriété, de télémétrie et de commande dans le contrôleur de température apparaissent dans le **Composant par défaut** de niveau supérieur. Les définitions de propriété, de télémétrie et de commande de chaque thermostat apparaissent dans les définitions de composant :

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Capture d’écran montrant le modèle d’appareil de contrôleur de température dans IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Capture d’écran montrant les composants de thermostat dans le modèle d’appareil de contrôleur de température dans IoT Central.":::

Pour savoir comment écrire le code de l’appareil qui interagit avec les composants, consultez le [Guide du développeur d’appareils IoT Plug-and-Play](concepts-developer-guide-device.md).

Pour savoir comment écrire le code de service qui interagit avec des composants sur un appareil, consultez le [Guide du développeur du service IoT Plug-and-Play](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Héritage

L’héritage vous permet de réutiliser des fonctionnalités dans des interfaces de base afin d’étendre les fonctionnalités d’une interface. Par exemple, plusieurs modèles d’appareils peuvent partager des fonctionnalités communes comme un numéro de série :

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Exemple d’héritage dans un modèle d’appareil montrant un thermostat et un contrôleur de flux qui partagent des fonctionnalités d’une interface de base." border="false":::

L’extrait de code suivant montre un modèle DTML qui utilise le mot clé `extends` pour définir la relation d’héritage présentée dans le diagramme précédent :

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

La capture d’écran suivante montre ce modèle dans l’environnement de modèle d’appareil IoT Central :

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Capture d’écran montrant l’héritage d’interface dans IoT Central":::

Lorsque vous écrivez du code d’appareil ou côté service, votre code n’a rien à faire de spécial pour gérer les interfaces héritées. Dans l’exemple présenté dans cette section, le code de votre appareil signale le numéro de série comme s’il faisait partie de l’interface de thermostat.

### <a name="tips"></a>Conseils

Vous pouvez combiner des composants et l’héritage lorsque vous créez un modèle. Le diagramme suivant illustre un modèle `thermostat` qui hérite d’une interface `baseDevice`. L'interface `baseDevice` possède un composant, qui hérite lui-même d’une autre interface :

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagramme montrant un modèle qui utilise des composants et l’héritage." border="false":::

L’extrait de code suivant montre un modèle DTML qui utilise les mots clés `extends` et `component` pour définir la relation d’héritage et l’utilisation de composant présentées dans le diagramme précédent :

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Types de données

Utilisez des types de données pour définir des paramètres de données de télémétrie, de propriétés et de commande. Les types de données peuvent être primitifs ou complexes. Les types de données complexes utilisent des primitives ou d’autres types complexes. La profondeur maximale pour les types complexes est de cinq niveaux.

### <a name="primitive-types"></a>Types primitifs

Le tableau suivant présente l’ensemble de types primitifs que vous pouvez utiliser :

| Type primitif | Description |
| --- | --- |
| `boolean` | Une valeur booléenne |
| `date` | Une date complète telle que définie dans la [section 5.6 de la RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Une date/heure telle que définie dans la [RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Une virgule flottante IEEE sur 8 octets |
| `duration` | Une durée au format ISO 8601 |
| `float` | Une virgule flottante IEEE sur 4 octets |
| `integer` | Un entier signé sur 4 octets |
| `long` | Un entier signé sur 8 octets |
| `string` | Une chaîne UTF8 |
| `time` | Une heure complète telle que définie dans la [section 5.6 de la RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

L’extrait de code suivant montre un exemple de définition de télémétrie qui utilise le type `double` dans le champ `schema` :

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Types de données complexes

Les types de données complexes sont un *tableau*, une *énumération*, un *mappage*, un *objet* ou l’un des types géospatiaux.

#### <a name="arrays"></a>Tableaux

Un tableau est un type de données indexable dans lequel tous les éléments sont du même type. Le type d’élément peut être primitif ou complexe.

L’extrait de code suivant montre un exemple de définition de télémétrie qui utilise le type `Array` dans le champ `schema`. Les éléments du tableau sont des valeurs booléennes :

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Énumérations

Une énumération décrit un type avec un ensemble d’étiquettes nommées qui mappent à des valeurs. Les valeurs peuvent être des entiers ou des chaînes, mais les étiquettes sont toujours des chaînes.

L’extrait de code suivant montre un exemple de définition de télémétrie qui utilise le type `Enum` dans le champ `schema`. Les valeurs de l’énumération sont des entiers :

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Cartes

Un mappage est un type avec des paires clé-valeur où les valeurs ont toutes le même type. La clé d’un mappage doit être une chaîne. Les valeurs d’un mappage peuvent être de n’importe quel type, y compris un autre type complexe.

L’extrait de code suivant montre un exemple de définition de propriété qui utilise le type `Map` dans le champ `schema`. Les valeurs du mappage sont des chaînes :

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objets

Un type d’objet est constitué de champs nommés. Les types des champs d’un mappage d’objets peuvent être primitifs ou complexes.

L’extrait de code suivant montre un exemple de définition de télémétrie qui utilise le type `Object` dans le champ `schema`. Les champs de l’objet sont `dateTime`, `duration` et `string` :

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Types géospatiaux

DTDL fournit un ensemble de types géospatiaux, basés sur [GeoJSON](https://geojson.org/), pour la modélisation des structures de données géographiques : `point`, `multiPoint`, `lineString`, `multiLineString`, `polygon` et `multiPolygon`. Ces types sont des structures imbriquées prédéfinies de tableaux, d’objets et d’énumérations.

L’extrait de code suivant montre un exemple de définition de télémétrie qui utilise le type `point` dans le champ `schema` :

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Étant donné que les types géospatiaux sont basés sur un tableau, ils ne peuvent pas être utilisés actuellement dans des définitions de propriété.

## <a name="semantic-types"></a>Types de sémantiques

Le type de données d’une définition de propriété ou de télémétrie spécifie le format des données qu’un appareil échange avec un service. Le type de sémantique fournit des informations sur les données de télémétrie et les propriétés qu’une application peut utiliser pour déterminer comment traiter ou afficher une valeur. Une ou plusieurs unités sont associées à chaque type de sémantique. Par exemple, les degrés Celsius et Fahrenheit sont des unités pour le type de sémantique de température. Les tableaux de bord et l’analytique d’IoT Central peuvent utiliser les informations du type de sémantique pour déterminer comment tracer les valeurs de télémétrie ou de propriété et afficher les unités. Pour savoir comment vous pouvez utiliser l’analyseur de modèle pour lire les types de sémantiques, consultez [Présentation de l’analyseur de modèle de jumeaux numériques](concepts-model-parser.md).

L’extrait de code suivant montre un exemple de définition de télémétrie qui comprend des informations sur le type de sémantique. Le type de sémantique `Temperature` est ajouté au tableau `@type`, et la valeur de `unit`, `degreeCelsius`, est l’une des unités valides pour le type de sémantique :

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Localisation

Les applications, telles que IoT Central, utilisent les informations du modèle pour créer dynamiquement une interface utilisateur autour des données échangées avec un appareil IoT Plug-and-Play. Par exemple, les vignettes d’un tableau de bord peuvent afficher les noms et les descriptions des données de télémétrie, des propriétés et des commandes.

Les champs `description` et `displayName` facultatifs dans le modèle contiennent des chaînes destinées à être utilisées dans une interface utilisateur. Ces champs peuvent contenir des chaînes localisées qu’une application peut utiliser pour afficher une interface utilisateur localisée.

L’extrait de code suivant montre un exemple de définition de télémétrie de température qui comprend des chaînes localisées :

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

L’ajout de chaînes localisées est facultatif. L’exemple suivant n’a qu’une seule langue par défaut :

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Cycle de vie et outils

Les quatre étapes du cycle de vie d’un modèle d’appareil sont la création, la publication, l’utilisation et le contrôle de version :

### <a name="author"></a>Auteur

Les modèles d’appareils DTML sont des documents JSON que vous pouvez créer dans un éditeur de texte. Toutefois, dans IoT Central, vous pouvez utiliser l’environnement de l’interface graphique utilisateur du modèle d’appareil pour créer un modèle DTML. Dans IoT Central vous pouvez :

- Créer des interfaces qui définissent des propriétés, des données de télémétrie et des commandes.
- Utiliser des composants pour assembler plusieurs interfaces.
- Définir des relations d’héritage entre les interfaces.
- Importer et exporter des fichiers de modèle DTML.

Pour en savoir plus, consultez [Définir un nouveau type d’appareil IoT dans votre application Azure IoT Central](../iot-central/core/howto-set-up-template.md).

L'[éditeur DTDL pour Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) vous offre un environnement d’édition textuel avec validation de la syntaxe et saisie semi-automatique pour un contrôle plus précis de l’expérience de création de modèles.

### <a name="publish"></a>Publish

Pour que vos modèles DTML puissent être partagés et découverts, vous les publiez dans un référentiel de modèles d’appareils.

Avant de publier un modèle dans le référentiel public, vous pouvez utiliser les outils `dmr-client` pour valider votre modèle.

Pour en savoir plus, consultez [Référentiel de modèles d’appareils](concepts-model-repository.md).

### <a name="use"></a>Utilisation

Les applications, telles que IoT Central, utilisent des modèles d’appareils. Dans IoT Central, un modèle fait partie du modèle d’appareil qui décrit les fonctionnalités de l’appareil. IoT Central utilise le modèle d’appareil pour créer dynamiquement une interface utilisateur pour l’appareil, y compris des tableaux de bord et des analyses.

Une solution personnalisée peut utiliser l'[analyseur de modèle de jumeaux numériques](concepts-model-parser.md) pour comprendre les fonctionnalités d’un appareil qui implémente le modèle. Pour en savoir plus, consultez [Utiliser les modèles IoT Plug-and-Play dans une solution IoT](concepts-model-discovery.md).

### <a name="version"></a>Version

Pour garantir que les appareils et les solutions côté serveur qui utilisent des modèles continuent de fonctionner, les modèles publiés sont non modifiables.

Le DTMI contient un numéro de version que vous pouvez utiliser pour créer plusieurs versions d’un modèle. Les appareils et les solutions côté serveur peuvent utiliser la version spécifique pour lesquels ils ont été conçus.

IoT Central implémente davantage de règles de contrôle de version pour les modèles d’appareils. Si vous contrôlez la version d’un modèle d’appareil et son modèle dans IoT Central, vous pouvez migrer des appareils de versions précédentes vers des versions ultérieures. Toutefois, les appareils migrés ne peuvent pas utiliser les nouvelles fonctionnalités sans une mise à niveau du microprogramme. Pour en savoir plus, consultez [Créer une nouvelle version de modèle d’appareil](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Limites et contraintes

La liste suivante résume certaines contraintes et limites principales sur les modèles :

- La profondeur maximale pour les tableaux, les mappages et les objets est actuellement de cinq niveaux.
- Vous ne pouvez pas utiliser de tableaux dans des définitions de propriété.
- Vous pouvez étendre les interfaces jusqu’à une profondeur de 10 niveaux.
- Une interface peut étendre au maximum deux autres interfaces.
- Un composant ne peut pas contenir un autre composant.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert la modélisation d’appareil, voici quelques ressources supplémentaires :

- [Installer et utiliser les outils de création DTDL](howto-use-dtdl-authoring-tools.md)
- [Langage DTDL v2 (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Référentiels de modèles](./concepts-model-repository.md)
