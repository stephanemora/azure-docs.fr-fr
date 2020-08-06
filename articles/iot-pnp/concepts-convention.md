---
title: Conventions IoT Plug-and-Play | Microsoft Docs
description: Description des conventions dont IoT Plug-and-Play attend des appareils qu’ils les utilisent lorsqu’ils envoient de la télémétrie et des propriétés et gèrent des commandes et des mises à jour des propriétés.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b06edb83e85ef6d56cf8bf8f91551fe99535eba1
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351969"
---
# <a name="iot-plug-and-play-conventions"></a>Conventions IoT Plug-and-Play

Les appareils IoT Plug-and-Play (préversion) doivent suivre un ensemble de conventions lorsqu’ils échangent des messages avec un hub IoT. Les appareils IoT Plug-and-Play (préversion) utilisent le protocole MQTT pour communiquer avec IoT Hub.

Vous décrivez la télémétrie, les propriétés et les commandes qu’un appareil IoT Plug-and-Play implémente à l’aide d’un _modèle_ [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Il existe deux types de modèles auxquels il est fait référence dans cet article :

- **Aucun composant** : modèle sans composants. Le modèle déclare la télémétrie, les propriétés et les commandes comme des propriétés de haut niveau dans la section Contenus de l’interface principale.
- **Plusieurs composants** : modèle composé d’au moins deux interfaces. Une interface principale avec une télémétrie, des propriétés et des commandes. Une ou plusieurs interfaces déclarées comme des composants avec une télémétrie, des propriétés et des commandes supplémentaires.

Pour plus d’informations, consultez [Composants IoT Plug-and-Play dans les modèles](concepts-components.md).

## <a name="model-discovery"></a>Découverte de modèles

Pour annoncer le modèle qu’il implémente, un appareil IoT Plug-and-Play inclut l’ID de modèle dans le paquet de connexion MQTT en ajoutant `model-id` au champ `USERNAME`.

Pour découvrir le modèle implémenté par un appareil, un service peut récupérer l’ID de modèle à partir :

- du champ `modelId` du jumeau d’appareil ;
- du champ `$metadata.$model` du jumeau numérique ;
- d’une notification de modification du jumeau numérique.

## <a name="telemetry"></a>Télémétrie

La télémétrie envoyée à partir d’un appareil sans composant ne nécessite aucune métadonnée supplémentaire. Le système ajoute la propriété `dt-dataschema`.

La télémétrie envoyée à partir d’un appareil à plusieurs composants doit ajouter `$.sub` comme propriété de message. Le système ajoute les propriétés `dt-subject` et `dt-dataschema`.

## <a name="read-only-properties"></a>Propriétés en lecture seule

### <a name="sample-no-component-read-only-property"></a>Exemple de propriété en lecture seule sans composant

Un appareil peut envoyer tout JSON valide qui respecte les règles DTDL v2.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Exemple de charge utile**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Exemple de propriété en lecture seule avec plusieurs composants

L’appareil doit ajouter le marqueur `{"__t": "c"}` pour indiquer que l’élément fait référence à un composant.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Propriété rapportée**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Propriétés accessibles en écriture

L’appareil doit confirmer qu’il a reçu la propriété en envoyant une propriété rapportée. La propriété rapportée doit inclure :

- `value` : valeur reçue par l’appareil.
- `ac` : code d’accusé de réception qui utilise un code d’état HTTP.
- `av` : version d’accusé de réception qui fait référence à la `$version` de la propriété souhaitée.
- `ad` : description facultative de l’accusé de réception.

### <a name="sample-no-component-writable-property"></a>Exemple de propriété accessible en écriture sans composant

Un appareil peut envoyer tout JSON valide qui respecte les règles DTDL v2 :

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Propriété souhaitée**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Propriété rapportée**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Exemple de propriété accessible en écriture avec plusieurs composants

L’appareil doit ajouter le marqueur `{"__t": "c"}` pour indiquer que l’élément fait référence à un composant.

Le marqueur est envoyé uniquement pour les mises à jour au niveau des composants, de sorte que les appareils ne doivent pas vérifient cet indicateur.

L’appareil doit confirmer qu’il a reçu la propriété en envoyant une propriété rapportée :

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Propriété souhaitée**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Propriété rapportée**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Commandes

Les interfaces sans composant utilisent le nom de commande sans préfixe.

Sur un appareil, les interfaces avec plusieurs composants utilisent des noms de commande au format suivant : `componentName*commandName`.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous connaissez les conventions IoT Plug-and-Play, voici quelques ressources supplémentaires :

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK d’appareils C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Composants de modèle](./concepts-components.md)
