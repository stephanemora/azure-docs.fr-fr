---
title: Ajouter un marqueur HTML dans Azure Maps | Microsoft Docs
description: Comment ajouter un marqueur HTML à une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5df3b9541dafad64e012519802b09b19ef199a03
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231732"
---
# <a name="add-html-markers-to-the-map"></a>Ajouter des marqueurs HTML à une carte

Cet article vous montre comment ajouter du code HTML personnalisé (comme un fichier image) à une carte, sous la forme d’un marqueur HTML.

> [!NOTE]
> Les marqueurs HTML ne se connectent pas aux sources de données. Au lieu de cela, des informations de position sont ajoutées directement au marqueur, qui est à son tour ajouté à la propriété `markers` de la carte, [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Contrairement à la plupart des couches des contrôles web Azure Maps qui utilisent WebGL pour le rendu, les marqueurs HTML utilisent des éléments DOM traditionnels. Par conséquent, plus le nombre de marqueurs HTML ajoutés à la page est important, plus il y aura d’éléments DOM. Les performances peuvent se dégrader si vous ajoutez plusieurs centaines de marqueurs HTML. Pour les jeux de données volumineux, envisagez le clustering de vos données ou l’utilisation d’une couche de symboles ou de bulles.

## <a name="add-an-html-marker"></a>Ajouter un marqueur HTML

La classe HtmlMarker a un style par défaut. Vous pouvez personnaliser le marqueur à l’aide des options de couleur et de texte. Le style par défaut de la classe HtmlMarker est un modèle SVG qui comprend un espace réservé de couleur et de texte. Définissez les propriétés de couleur et de texte dans les options HtmlMarker pour une personnalisation rapide. 

<br/>

<iframe height='500' scrolling='no' title='Ajouter un marqueur HTML à une carte' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add an HTML Marker to a map</a> (Ajouter un marqueur HTML à une carte) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code ajoute un [marqueur Html](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) à la carte en utilisant la propriété [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) de la classe [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Le marqueur HTML est ajouté à la carte dans la fonction d’[écouteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events), pour garantir qu’il s’affiche après le chargement complet de la carte.

## <a name="create-svg-templated-html-marker"></a>Créer un marqueur HTML basé sur un modèle SVG

Le `htmlContent` par défaut d’un marqueur HTML est un modèle SVG contenant les espaces réservés `{color}` et `{text}`. Vous pouvez créer des chaînes SVG personnalisées et ajouter ces mêmes espaces réservés dans votre modèle SVG, de sorte que la définition des options `color` et `text` du marqueur mette à jour ces espaces réservés dans votre modèle SVG.

<br/>

<iframe height='500' scrolling='no' title='Marqueur HTML avec modèle SVG personnalisé' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML Marker with Custom SVG Template</a> (Marqueur HTML avec modèle SVG personnalisé) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Ajouter un marqueur HTML avec style CSS

L’un des avantages des marqueurs HTML est que vous pouvez utiliser de nombreuses personnalisations intéressantes à l’aide de feuilles de style en cascade. Dans cet exemple, le contenu de HtmlMarker se compose de code HTML et de feuilles de style en cascade qui créent une épingle animée qui « pulse » une fois en place.

<br/>

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> (Source de données HTML) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Marqueurs HTML déplaçables

Cet exemple montre comment rendre un marqueur HTML déplaçable. Les marqueurs HTML prennent en charge les événements `drag`, `dragstart` et `dragend`.

<br/>

<iframe height='500' scrolling='no' title='Marqueur HTML déplaçable' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Draggable HTML Marker</a> (Marqueur HTML déplaçable) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Ajouter des événements de souris aux marqueurs HTML

Ces exemples montrent comment ajouter des événements de souris à un marqueur HTML en ajoutant des événements JavaScript standard au contenu HTML du marqueur. 

Si le `htmlContent` du marqueur est un élément DOM (div, img...), vous pouvez y ajouter des événements directement. 

<br/>

<iframe height='500' scrolling='no' title='Ajout d’événements de souris aux marqueurs HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adding Mouse Events to HTML Markers</a> (Ajout d’événements de souris aux marqueurs HTML) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Si le `htmlContent` est une chaîne (c’est-à-dire la valeur par défaut), vous pouvez créer un élément DOM et ajouter la chaîne en tant qu’innerHTML à celui-ci, puis ajouter l’élément DOM en tant que htmlContent. Si le `htmlContent` est une chaîne basée sur un modèle, vous devrez d’abord rechercher/remplacer les valeurs `{text}` et `{color}` dans la chaîne.

<br/>

<iframe height='500' scrolling='no' title='Ajout d’événements de souris au marqueur HTML par défaut' src='//codepen.io/azuremaps/embed/ZmZLBa/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/ZmZLBa/'>Adding Mouse Events to default HTML Marker</a> (Ajout d’événements de souris au marqueur HTML par défaut) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](./map-add-bubble-layer.md)