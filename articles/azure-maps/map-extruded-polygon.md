---
title: Ajouter une couche d’extrusion de polygone à une carte | Microsoft Azure Maps
description: Guide pratique pour ajouter une couche d’extrusion de polygone au kit SDK web Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: cd12242367c6f3b5b2c64df28f7f5e52cf1a4f97
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282872"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Ajouter une couche d’extrusion de polygone à la carte

Cet article explique comment utiliser la couche d’extrusion de polygone pour afficher des zones d’entités géométriques `Polygon` et `MultiPolygon` en tant que formes extrudées. Le Kit de développement logiciel (SDK) web Azure Maps prend également en charge le rendu de géométries Circle comme défini dans le [schéma GeoJSON étendu](extend-geojson.md#circle). Ces cercles peuvent être transformés en polygones une fois rendus sur la carte. Toutes les entités géométriques peuvent facilement être mises à jour lorsqu’elles sont enveloppées dans la classe [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

## <a name="use-a-polygon-extrusion-layer"></a>Utiliser une couche d’extrusion de polygone

Connectez la [couche d’extrusion de polygone](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) à une source de données. Ensuite, chargez-la sur la carte. La couche d’extrusion de polygone restitue les zones des entités `Polygon` et `MultiPolygon` en tant que formes extrudées. Les propriétés `height` et `base` de la couche d’extrusion de polygone définissent la distance de base par rapport au sol et la hauteur de la forme extrudée en **mètres**. Le code suivant montre comment créer un polygone, l’ajouter à une source de données et l’afficher à l’aide de la classe Couche d’extrusion de polygone.

> [!Note]
> La valeur de `base` définie dans la couche d’extrusion de polygone doit être inférieure ou égale à la valeur de `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polygone extrudé" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>Extruded polygon</a> (Polygone extrudé) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Ajouter des polygones pilotés par des données

Une carte choroplèthe peut être rendue à l’aide de la couche d’extrusion de polygone. Définissez les propriétés `height` et `fillColor` de la couche d’extrusion sur la mesure de la variable statistique dans les entités géométriques `Polygon` et `MultiPolygon`. L’exemple de code suivant montre une carte choroplèthe extrudée des États-Unis basée sur la mesure de la densité de population de chaque État.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carte choroplèthe extrudée" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded choropleth map</a> (Carte choroplèthe extrudée) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Ajouter un cercle à la carte

Azure Maps utilise une version étendue du schéma GeoJSON qui fournit une définition de cercles, comme indiqué [ici](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Un cercle extrudé peut être affiché sur la carte en créant une entité `point` avec une propriété `subType` dotée de la valeur `Circle` et une propriété `Radius` numérique représentant le rayon en **mètres**. Par exemple :

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Le Kit de développement logiciel (SDK) web Azure Maps convertit en coulisses ces entités `Point` en entités `Polygon`. Ces entités `Point` peuvent être rendues sur la carte à l’aide de la couche d’extrusion de polygone, comme illustré dans l’exemple de code suivant.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polygone d’espace aérien de drone" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>Drone airspace polygon</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personnaliser une couche d’extrusion de polygone

La couche d’extrusion de polygone comporte plusieurs options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Couche d’extrusion de polygone](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Ressources supplémentaires :

> [!div class="nextstepaction"]
> [Extension de spécification GeoJSON Azure Maps](extend-geojson.md#circle)
