---
title: Ajouter une forme avec Azure Maps | Microsoft Docs
description: Comment ajouter une forme à une carte Javascript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: fb9ec0713d3db465cf835346465e70c4455b38ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-shape-to-a-map"></a>Ajouter une forme à une carte

Cet article explique comment ajouter une ligne, un cercle et un polygone à la carte. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Ajouter une ligne

<iframe height='500' scrolling='no' title='Ajouter une ligne à une carte' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a> (Ajouter une ligne à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consultez [créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, une ligne est créée. Une ligne est un [Fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de LineString, LineStringProperties étant sa propriété Fonctionnalité. Utilisez `new atlas.data.Feature(new atlas.data.LineString())` pour créer une ligne et définir ses propriétés. 

Une couche de lignes est un tableau de lignes. Le dernier bloc de code utilise la fonction [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) de la classe map pour ajouter la couche de lignes à la carte et définir les propriétés de la couche de lignes. Consultez les propriétés d’une couche de lignes dans [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Ajouter un cercle

<iframe height='500' scrolling='no' title='Ajouter un cercle à une carte' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a> (Ajouter un cercle à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consultez [créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un cercle est créé. Un cercle est un [Fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de [Point](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest), [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) étant sa propriété Fonctionnalité. Utilisez `new atlas.data.Feature(new atlas.data.Point())` pour créer un cercle et définir ses propriétés.

Une couche de cercles est un tableau de cercles. Le dernier bloc de code utilise la fonction [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) de la classe map pour ajouter la couche de cercles à la carte et définir les propriétés de la couche de cercles. Consultez les propriétés d’une couche de cercles dans [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Ajouter un polygone
<iframe height='500' scrolling='no' title='Ajouter un polygone à une carte ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a> (Ajouter un polygone à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consultez [créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un polygone est créé. Un polygone est un [Fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de [Polygone](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest), [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) étant sa propriété Fonctionnalité. Utilisez `new atlas.data.Feature(new atlas.data.Polygon())` pour créer un polygone et définir ses propriétés. Fournissez des coordonnées ordonnées de chemin d’accès du polygone dans le constructeur de polygone.

Une couche de polygones est un tableau de polygones. Le dernier bloc de code utilise la fonction [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) de la classe map pour ajouter la couche de polygones à la carte et définir ses propriétés. Consultez les propriétés d’une couche de polygones dans [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest). 
