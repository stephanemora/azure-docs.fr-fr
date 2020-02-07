---
title: Ajouter des contrôles à une carte | Microsoft Azure Maps
description: Comment ajouter un contrôle de zoom, un contrôle de tonalité, un contrôle de rotation et un sélecteur de styles à une carte dans Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e2ad8eb181685d3ac3de8b1e0ed7ef8ddfa4e224
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933293"
---
# <a name="add-controls-to-a-map"></a>Ajouter des commandes à une carte

Cet article décrit comment ajouter des contrôles à une carte. Vous allez également apprendre à créer une carte avec tous les contrôles et un [sélecteur de styles](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Ajouter un contrôle de zoom

Un contrôle de zoom ajoute des boutons pour effectuer un zoom avant et arrière sur la carte. L’exemple de code suivant crée une instance de la classe [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol), puis l’ajoute dans l’angle inférieur droit de la carte.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Ajout d’un contrôle de zoom</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Ajouter un contrôle de tonalité

Un contrôle de pas ajoute des boutons pour faire pivoter le pas de la carte par rapport à l’horizon. L’exemple de code suivant crée une instance de la classe [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol). Il ajoute le PitchControl dans l’angle supérieur droit de la carte.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de tonalité' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Ajout d’un contrôle de tonalité</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Ajouter un contrôle de boussole

Un contrôle de boussole ajoute un bouton pour faire pivoter la carte. L’exemple de code suivant crée une instance de la classe [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol) et l’ajoute à l’angle inférieur gauche de la carte.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de rotation' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Ajout d’un contrôle de rotation</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Une carte avec tous les contrôles

L’exemple de code suivant ajoute les contrôles de sélecteur de styles, de zoom, de pas et de boussole au coin inférieur droit de la carte. Notez la manière dont elles s’empilent automatiquement. L’ordre des objets de contrôle dans le script détermine l’ordre dans lequel ils apparaissent sur la carte. Pour modifier l’ordre des commandes sur la carte, vous pouvez changer leur ordre dans le script.

<br/>

<iframe height='500' scrolling='no' title='Une carte avec tous les contrôles' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Une carte avec tous les contrôles</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le contrôle de sélecteur de styles est défini par la classe [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol). Pour plus d’informations sur l’utilisation du contrôle de sélecteur de styles, consultez [choisir un style de carte](choose-map-style.md).

## <a name="customize-controls"></a>Personnaliser des contrôles

Voici un outil permettant de tester les différentes options de personnalisation des contrôles.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Options de contrôle de navigation" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Navigation control options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Si vous souhaitez créer des contrôles de navigation personnalisés, créez une classe qui s’étend à partir de la classe `atlas.Control` ou créez un élément HTML et positionnez-le au-dessus de la balise div de la carte. Faire en sorte que ce contrôle d’interface utilisateur appelle la fonction `setCamera` des carte pour déplacer la carte. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Contrôle de boussole](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Pour voir des codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter un repère](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter une fenêtre contextuelle](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](map-add-shape.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

