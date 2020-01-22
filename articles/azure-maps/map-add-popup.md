---
title: Ajouter une fenêtre contextuelle à un point sur une carte | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à ajouter une fenêtre contextuelle à un point à l’aide du Kit de développement logiciel (SDK) Web Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 79bafb331cb7ad38ea7cad9e510b22886b647764
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911147"
---
# <a name="add-a-popup-to-the-map"></a>Ajouter une fenêtre contextuelle à la carte

Cet article vous explique comment ajouter une fenêtre contextuelle à un point sur une carte.

## <a name="understand-the-code"></a>Comprendre le code

Le code suivant ajoute une caractéristique de point, qui a des propriétés `name` et `description`, à la carte à l’aide d’un calque de symboles. Une instance de la [classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) est créée, mais elle n’est pas affichée. Des événements de souris sont ajoutés au calque de symboles pour déclencher l’ouverture et la fermeture de la fenêtre contextuelle quand la souris pointe sur le marqueur de symbole. Quand vous survolez le symbole de marqueur, la propriété `position` de la fenêtre contextuelle est mise à jour avec la position du marqueur et l’option `content` est mise à jour avec du code HTML qui encapsule les propriétés `name` et `description` de la caractéristique de point survolée. La fenêtre contextuelle est ensuite affichée sur la carte à l’aide de sa fonction `open`.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Ajouter une fenêtre contextuelle à l’aide d’Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Affichez l’objet Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Ajouter une fenêtre contextuelle à l’aide d’Azure Maps</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Réutilisation d’une fenêtre contextuelle avec plusieurs points

Si vous disposez d’un grand nombre de points et si vous souhaitez n’afficher qu’une seule fenêtre contextuelle à la fois, la meilleure solution consiste à créer une fenêtre contextuelle et à la réutiliser plutôt que de créer une fenêtre pour chaque caractéristique de point. La réutilisation de la fenêtre contextuelle permet de réduire considérablement le nombre d’éléments DOM créés par l’application, ce qui peut améliorer les performances. L’exemple suivant crée des caractéristiques à trois points. Si vous cliquez sur l’un d’eux, une fenêtre contextuelle s’affiche avec le contenu de ce point.

<br/>

<iframe height='500' scrolling='no' title='Réutilisation d’une fenêtre contextuelle avec plusieurs épingles' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pin</a> (Réutilisation d’une fenêtre contextuelle avec plusieurs épingles) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personnalisation d’une fenêtre contextuelle

Par défaut, la fenêtre contextuelle a un arrière-plan blanc, une flèche de pointeur en bas et un bouton Fermer dans le coin supérieur droit. L’exemple suivant change la couleur d’arrière-plan en noir à l’aide de l’option `fillColor` de la fenêtre contextuelle. Le bouton Fermer est supprimé en affectant la valeur false à l’option `shoCloseButton`. Le contenu HTML de la fenêtre contextuelle est placé à 10 pixels des bords de la fenêtre contextuelle, et le texte est affiché en blanc pour une meilleure lisibilité sur l’arrière-plan noir.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fenêtre contextuelle personnalisée" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Customized Popup</a> (Fenêtre contextuelle personnalisée) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Événements de fenêtres contextuelles

Les fenêtres contextuelles peuvent être ouvertes, fermées et déplacées. La classe popup fournit des événements pour aider les développeurs à réagir à ces actions. L’exemple suivant met en évidence les événements qui se déclenchent quand vous ouvrez, fermez ou faites glisser la fenêtre contextuelle. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Événements de fenêtres contextuelles" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>Popup events</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants qui sont très intéressants :

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter un marqueur HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](map-add-shape.md)