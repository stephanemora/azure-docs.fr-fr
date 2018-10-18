---
title: Créer une carte avec Azure Maps | Microsoft Docs
description: Procédure de création d’une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730007"
---
# <a name="create-a-map"></a>Créer une carte

Cet article explique comment créer une carte.  

## <a name="understand-the-code"></a>Comprendre le code

Il existe deux moyens de créer une carte. Vous pouvez définir la caméra de la carte en spécifiant le point central et le niveau du zoom. Définissez les limites de la caméra de la map en spécifiant les points de limitation sud-ouest et nord-est.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Définir la caméra

<iframe height='310' scrolling='no' title='Créer une carte via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` (Créer une carte via) </a>par Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) est créé via `new atlas.Map()`. Les propriétés de la carte, comme le point central et le niveau de zoom font partie de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` peut être défini dans le constructeur de la carte ou via la fonction [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) de la classe map.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Définir les limites de la caméra

<iframe height='310' scrolling='no' title='Créer une carte via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a>par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) est construit via `new atlas.Map()`. Les propriétés de la carte, comme le cadre englobant, font partie de [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). `CameraBoundsOptions` peut être défini via la fonction [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) de la classe map.

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
