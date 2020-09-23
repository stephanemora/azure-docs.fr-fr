---
title: Présentation des composants dans les modèles IoT Plug-and-Play | Microsoft Docs
description: Comprenez les différences entre les modèles IoT Plug-and-Play DTDL qui utilisent des composants et des modèles qui n’utilisent pas de composants.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c4a32a5c929e74332e85ceb6f4cff787e237e385
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069643"
---
# <a name="iot-plug-and-play-components-in-models"></a>Composants IoT Plug-and-Play dans les modèles

Dans les conventions IoT Plug-and-Play, un appareil est un appareil IoT Plug-and-Play s’il présente son ID de modèle DTDL (Digital Twins Definition Language) lorsqu’il se connecte à un hub IoT.

L’extrait de code suivant montre quelques exemples d’ID de modèle :

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Aucun composant

Un modèle simple n’utilise pas de composants incorporés ou en cascade. Il comprend des informations d’en-tête et une section de contenu pour définir la télémétrie, les propriétés et les commandes.

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

Bien que le modèle ne définisse pas explicitement de composant, il se comporte comme s’il y avait un seul composant avec toutes les définitions de télémétrie, de propriété et de commande.

La capture d’écran suivante montre la façon dont le modèle s’affiche dans l’outil Explorateur Azure IoT :

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Composant par défaut dans l’explorateur Azure IoT":::

L’ID de modèle est stocké dans une propriété de jumeau d’appareil, comme le montre la capture d’écran suivante :

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="ID de modèle dans la propriété du jumeau numérique":::

Un modèle DTDL sans composants est une simplification utile pour un appareil doté d’un ensemble unique de télémétrie, de propriétés et de commandes. Un modèle qui n’utilise pas de composants facilite la migration d’un appareil existant en appareil IoT Plug-and-Play : vous créez un modèle DTDL qui décrit votre appareil réel sans avoir à définir de composants.

## <a name="multiple-components"></a>Plusieurs composants

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
...
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
...
```

Ce modèle a trois composants définis dans la section de contenu : deux composants `Thermostat` et un composant `DeviceInformation`. Il y a également un composant racine par défaut.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert les composants de modèle, voici quelques ressources supplémentaires :

- [Langage DTDL v2 (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Référentiels de modèles](./concepts-model-repository.md)