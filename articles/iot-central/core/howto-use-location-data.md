---
title: Utiliser des données d’emplacement dans une solution Azure IoT Central
description: Découvrez comment utiliser les données d’emplacement envoyées à partir d’un appareil connecté à votre application IoT Central. Tracer les données d’emplacement sur une carte ou créer des règles de geofencing.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e93da2bec3753ba6efda2b4eb16d6ef78b42234b
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108747212"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Utiliser des données d’emplacement dans une solution Azure IoT Central

Cet article explique comment utiliser des données d’emplacement dans une application IoT Central. Un appareil connecté à IoT Central peut envoyer des données d’emplacement sous forme de flux de télémétrie ou utiliser une propriété d’appareil pour signaler des données d’emplacement.

Vous pouvez utiliser les données de localisation pour :

* Tracer l’emplacement signalé sur une carte.
* Tracer l’historique des emplacements de télémétrie sur une carte.
* Créer des règles de geofencing pour notifier un opérateur lorsqu’un appareil entre dans une zone spécifique ou en sort.

## <a name="add-location-capabilities-to-a-device-template"></a>Ajouter des fonctionnalités d’emplacement à un modèle d’appareil

La capture d’écran suivante montre un modèle d’appareil avec des exemples de propriété d’appareil et de type de télémétrie utilisant des données d’emplacement. Les définitions utilisent le type sémantique **location** et le type de schéma **geolocation** :

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Capture d’écran montrant la définition de la propriété d’emplacement dans le modèle d’appareil":::

Pour référence, les définitions [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de ces fonctionnalités ressemblent à l’extrait de code suivant :

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> Le type de schéma **geopoint** ne fait pas partie de la [spécification DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Actuellement, IoT Central prend en charge le type de schéma **geopoint** et le type sémantique **location** à des fins de compatibilité descendante.

## <a name="send-location-data-from-a-device"></a>Envoyer des données d’emplacement à partir d’un appareil

Lorsqu’un appareil envoie des données pour la propriété **DeviceLocation** présentée dans la section précédente, la charge utile ressemble à l’extrait de code JSON suivant :

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Lorsqu’un appareil envoie des données pour la télémétrie de **suivi** présentée dans la section précédente, la charge utile ressemble à l’extrait de code JSON suivant :

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Afficher l'emplacement de l’appareil

Vous pouvez afficher des données d’emplacement à plusieurs endroits de votre application IoT Central. Par exemple, sur des vues associées à un appareil individuel ou sur des tableaux de bord.

Lorsque vous créez une vue pour un appareil, vous pouvez choisir de tracer l’emplacement sur une carte ou d’afficher les valeurs individuelles :

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Capture d’écran montrant un exemple de vue avec des données d’emplacement":::

Vous pouvez ajouter des mosaïques cartographiques à un tableau de bord pour tracer l’emplacement d’un ou plusieurs appareils. Lorsque vous ajoutez une mosaïque cartographique pour afficher la télémétrie de l’emplacement, vous pouvez tracer l’emplacement sur une période de temps. La capture d’écran suivante montre l’emplacement signalé par un appareil simulé au cours des 30 dernières minutes :

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Capture d’écran montrant un exemple de tableau de bord avec des données d’emplacement":::

## <a name="create-a-geofencing-rule"></a>Créer une règle de geofencing

Vous pouvez utiliser la télémétrie d’emplacement pour créer une règle de geofencing générant une alerte lorsqu’un appareil se déplace dans une zone rectangulaire ou en sort. La capture d’écran suivante montre une règle utilisant quatre conditions pour définir une zone rectangulaire à l’aide des valeurs de latitude et de longitude. La règle génère un e-mail lorsque l’appareil se déplace dans la zone rectangulaire :

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Capture d’écran montrant une définition de règle de geofencing":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser les propriétés dans votre application Azure IoT Central, consultez :

* [Payloads](concepts-telemetry-properties-commands.md)
* [Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md)