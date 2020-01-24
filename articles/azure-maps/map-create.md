---
title: Créer une carte avec Azure Maps | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à afficher une carte dans une page web à l’aide du SDK web Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 49c86f3e6c654ecbfcd07809f42a1b038ca3f8ab
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911105"
---
# <a name="create-a-map"></a>Créer une carte

Cet article vous montre comment créer et animer une carte.  

## <a name="loading-a-map"></a>Chargement d’une carte

Pour charger une carte, créez une nouvelle instance de la [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Lors de l’initialisation de la carte, un ID d’élément DIV pour afficher la carte et un ensemble d’options à utiliser lors du chargement de la carte sont transmis. Si les informations d’authentification par défaut ne sont pas spécifiées sur l’espace de noms `atlas`, vous devez les spécifier dans les options de carte lors du chargement de la carte. Pour de meilleures performances, la carte charge plusieurs ressources de manière asynchrone. Ainsi, après avoir créé l’instance de carte, attachez un événement `ready` ou `load` à la carte, puis ajoutez tout autre code qui interagit avec la carte dans ce gestionnaire d’événements. L’événement `ready` est déclenché dès qu’un nombre suffisant de ressources avec lesquelles interagir de manière programmatique est chargé sur la carte. L’événement `load` est déclenché après la fin du chargement complet de la vue de carte initiale. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Chargement de carte de base" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Plusieurs cartes peuvent être chargées sur la même page et chacune d’entre elles peut utiliser des paramètres d’authentification et de langue identiques ou différents.

## <a name="show-a-single-copy-of-the-world"></a>Afficher une copie unique du monde

Lorsque vous effectuer un zoom arrière sur la carte sur un écran étendu, plusieurs copies du monde s’affichent horizontalement. Ceci est parfait pour la plupart des scénarios, mais pour certaines applications, il peut être souhaitable de ne voir qu’une seule copie du monde. Pour cela, vous devez configurer l’option `renderWorldCopies` des cartes sur `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Contrôle de la caméra de carte

Vous pouvez définir la zone affichée de la carte à l’aide de la caméra de deux manières différentes. Vous pouvez définir des options de caméra, telles que le centre et le zoom, lors du chargement de la carte, ou appeler l’option `setCamera` à tout moment après le chargement de la carte afin de mettre à jour la vue de carte de manière programmatique.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Définir la caméra

Dans le code suivant, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) est créé et les options de centre et de zoom sont définies. Les propriétés de la carte, comme le centre et le niveau de zoom, font partie des [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Créer une carte via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` (Créer une carte via) </a>d’Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Définir les limites de la caméra

Dans le code suivant, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) est construit via `new atlas.Map()`. Des propriétés de carte, telles que `CameraBoundsOptions`, peuvent être définies avec la fonction [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de la classe Map. Les propriétés des marges intérieures et des limites sont définies au moyen de `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Créer une carte via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a>d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animer la vue cartographique

Dans le code suivant, le premier bloc de code crée une carte et définit les valeurs de style de carte, de centre et de zoom. Dans le deuxième bloc de code, un gestionnaire d’événements de clics est créé pour le bouton d’animation. Lorsque vous cliquez sur ce bouton, la fonction setCamera est appelée avec des valeurs aléatoires pour [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animer la vue cartographique' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animer la vue cartographique</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Essayer le code

Examinez l’exemple de code ci-dessus. Vous pouvez modifier le code JavaScript de l’**onglet JS** sur la gauche, et observer les modifications de la vue de carte sur l’**onglet des résultats** sur la droite. Vous pouvez également cliquer sur le bouton de **modification sur CodePen** afin de modifier le code par ce biais.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

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