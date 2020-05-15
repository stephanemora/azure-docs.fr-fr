---
title: Créer une carte avec Azure Maps | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à afficher une carte dans une page web à l’aide du Kit de développement logiciel (SDK) web Microsoft Azure Maps.
author: Philmea
ms.author: philmea
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97eb1ebb61e5ff78ed918fded8107f5775b533c2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124021"
---
# <a name="create-a-map"></a>Créer une carte

Cet article vous montre comment créer et animer une carte.  

## <a name="loading-a-map"></a>Chargement d’une carte

Pour charger une carte, créez une nouvelle instance de la [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Lors de l’initialisation de la carte, transmettez un ID d’élément DIV pour afficher la carte et transmettez un ensemble d’options à utiliser lors du chargement de la carte. Si les informations d’authentification par défaut ne sont pas spécifiées sur l’espace de noms `atlas`, vous devez les spécifier dans les options de carte lors du chargement de la carte. Pour de meilleures performances, la carte charge plusieurs ressources de manière asynchrone. Ainsi, après avoir créé l’instance de carte, attachez un événement `ready` ou `load` à la carte, puis ajoutez tout autre code qui interagit avec la carte dans le gestionnaire d’événements. L’événement `ready` est déclenché dès qu’un nombre suffisant de ressources avec lesquelles interagir de manière programmatique est chargé sur la carte. L’événement `load` est déclenché après la fin du chargement complet de la vue de carte initiale. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Chargement de carte de base" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Vous pouvez charger plusieurs cartes sur la même page. Plusieurs cartes sur la même page peuvent utiliser des paramètres d’authentification et de langue identiques ou différents.

## <a name="show-a-single-copy-of-the-world"></a>Afficher une copie unique du monde

Lorsque vous effectuer un zoom arrière sur la carte sur un écran étendu, plusieurs copies du monde s’affichent horizontalement. Cette option est idéale pour certains scénarios, mais pour certaines applications, il est souhaitable de voir une seule copie du monde. Ce comportement est implémenté en définissant l’option `renderWorldCopies` de la carte sur `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Options de carte

Lors de la création d’une carte, plusieurs types d’options peuvent être passés pour personnaliser le fonctionnement de la carte, comme indiqué ci-dessous.

- Les options [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) et [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) sont utilisées pour spécifier la zone que la carte doit s’afficher.
- L’option [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) permet de spécifier la façon dont la carte doit interagir avec les services qui l’alimentent.
- L’option [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) permet de spécifier qu’un style et un rendu doivent être appliqués à la carte.
- L’option [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) permet de spécifier la façon dont la carte doit s’afficher lorsque l’utilisateur interagit avec elle. 

Ces options peuvent également être mises à jour après le chargement de la carte à l’aide des fonctions `setCamera`, `setServiceOptions`, `setStyle` et `setUserInteraction`. 

## <a name="controlling-the-map-camera"></a>Contrôle de la caméra de carte

Vous pouvez définir la zone affichée de la carte à l’aide de la caméra de deux manières différentes. Vous pouvez définir les options de la caméra lors du chargement de la carte. Vous pouvez également appeler l’option `setCamera` à tout moment après le chargement de la carte pour mettre à jour l’affichage cartographique par programmation.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Définir la caméra

La caméra de la carte contrôle ce qui est affiché dans la fenêtre d’affichage du canevas de la carte. Les options de la caméra peuvent être transmises lorsqu’elles sont initialisées ou passées dans la fonction `setCamera`.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

Dans le code suivant, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) est créé et les options de centre et de zoom sont définies. Les propriétés de la carte, comme le centre et le niveau de zoom, font partie des [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Créer une carte via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` (Créer une carte via) </a>d’Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Définir les limites de la caméra

Un cadre englobant peut être utilisé pour mettre à jour la caméra de la carte. Si le cadre englobant a été calculé à partir de données de point, il est souvent utile de spécifier également une valeur de remplissage des pixels dans les options de la caméra pour tenir compte de la taille de l’icône. Cela permet de s’assurer que les points n’apparaissent pas en dehors du bord du point de vue de la carte.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

Dans le code suivant, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) est construit via `new atlas.Map()`. Des propriétés de carte, telles que `CameraBoundsOptions`, peuvent être définies avec la fonction [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) de la classe Map. Les propriétés des marges intérieures et des limites sont définies au moyen de `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Créer une carte via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a>d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animer la vue cartographique

Lorsque vous définissez les options de caméra de la carte, des [options d’animation](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) peuvent également être définies. Ces options spécifient le type d’animation et la durée nécessaire pour déplacer la caméra.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

Dans le code suivant, le premier bloc de code crée une carte et définit les styles d’entrée et de zoom de la carte. Dans le deuxième bloc de code, un gestionnaire d’événements de clics est créé pour le bouton d’animation. Lorsque vous cliquez sur ce bouton, la fonction `setCamera` est appelée avec des valeurs aléatoires pour [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) et [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animer la vue cartographique' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animer la vue cartographique</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Essayer le code

Examinez les exemples de code. Vous pouvez modifier le code JavaScript dans l’**onglet JS** et observer les modifications apportées à l’affichage cartographique sur l’**onglet Résultats**. Vous pouvez également cliquer sur **Modifier sur CodePen** dans l’angle supérieur droit afin de modifier le code par ce biais.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Consultez les exemples de code pour ajouter la fonctionnalité à votre application :

> [!div class="nextstepaction"]
> [Changer le style de la carte](choose-map-style.md)

> [!div class="nextstepaction"]
> [Ajouter des contrôles à la carte](map-add-controls.md)

> [!div class="nextstepaction"]
> [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-maps)
