---
title: Créer une carte avec Azure Maps | Microsoft Docs
description: Procédure de création d’une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699362"
---
# <a name="create-a-map"></a>Créer une carte

Cet article vous montre comment créer et animer une carte.  

## <a name="understand-the-code"></a>Comprendre le code

Il existe deux moyens de créer une carte. Vous pouvez définir la caméra sur la carte en spécifiant le point central et le niveau de zoom, ou paramétrer les limites de la caméra sur la carte en définissant les points de limitation sud-ouest et nord-est (southwest et northeast, respectivement).

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Définir la caméra

<iframe height='500' scrolling='no' title='Créer une carte via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` (Créer une carte via) </a>par Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) est créé par le biais de `new atlas.Map()`, dans lequel le centre est défini de même que le zoom. Les propriétés de la carte, comme le point central et le niveau de zoom font partie de [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Définir les limites de la caméra

<iframe height='500' scrolling='no' title='Créer une carte via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a>par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) est construit via `new atlas.Map()`. Des propriétés de carte, telles que `CameraBoundsOptions`, peuvent être définies avec la fonction [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de la classe Map. Les propriétés des marges intérieures et des limites sont définies au moyen de `setCamera`.

### <a name="animate-map-view"></a>Animer la vue cartographique

<iframe height='500' scrolling='no' title='Animer la vue cartographique' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animer la vue cartographique</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code crée un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) par l’intermédiaire de `new atlas.Map()`. Les propriétés de la carte, comme le point central et le niveau de zoom font partie de [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` peut être défini dans le constructeur de la carte ou via la fonction [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de la classe map. Le [style de carte](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) est défini sur `road`.

Le deuxième bloc de code crée une fonction d’animation de carte, qui donne vie aux changements dans la vue cartographique en définissant [AnimateOptions](/javascript/api/azure-maps-control/atlas.animationoptions) via la fonction [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). La fonction est déclenchée par le bouton « Animate Map (Animer la carte) » pour générer un niveau de zoom aléatoire à chaque clic.

## <a name="try-out-the-code"></a>Essayer le code

Examinez l’exemple de code ci-dessus. Vous pouvez modifier le code JavaScript de l’**onglet JS** sur la gauche, et observer les modifications de la vue de carte sur l’**onglet des résultats** sur la droite. Vous pouvez également cliquer sur le bouton de **modification sur CodePen** afin de modifier le code par ce biais.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consultez les exemples de code pour ajouter la fonctionnalité à votre application :

> [!div class="nextstepaction"]
> [Choisir un style de carte](choose-map-style.md)

> [!div class="nextstepaction"]
> [Ajouter des contrôles de carte](map-add-controls.md)
