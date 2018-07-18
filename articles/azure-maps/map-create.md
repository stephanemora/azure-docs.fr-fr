---
title: Créer une carte avec Azure Maps | Microsoft Docs
description: Procédure de création d’une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec85854e5d9b7ee0d5e2c54881a417ba6cbb366e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599811"
---
# <a name="create-a-map"></a>Créer une carte

Cet article explique comment créer une carte.  

## <a name="understand-the-code"></a>Comprendre le code

Il existe deux moyens de créer une carte. Vous pouvez définir la caméra sur la carte en spécifiant le point central et le niveau de zoom, ou paramétrer les limites de la caméra sur la carte en définissant les points englobants associés au sud-ouest et au nord-est (southwest et northeast, respectivement).

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Définition de la caméra

<iframe height='310' scrolling='no' title='Créer une carte via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via CameraOptions (Créer une carte via CameraOptions)</a> par Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) est créé via `new atlas.Map()`. Les propriétés de la carte, comme le point central et le niveau de zoom font partie de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest). CameraOptions peut être défini dans le constructeur de la carte ou via la fonction [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) de la classe map.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Définition des limites de la caméra

<iframe height='310' scrolling='no' title='Créer une carte via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via CameraBoundsOptions (Créer une carte via CameraBoundsOptions)</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, un [objet de carte](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) est construit via `new atlas.Map()`. Les propriétés de la carte, comme le cadre englobant, font partie de [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions peut être défini via la fonction [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) de la classe map.

## <a name="try-out-the-code"></a>Essayer le code 

Examinez l’exemple de code ci-dessus. Vous pouvez modifier le code JavaScript de l’onglet JS sur la gauche, et observer les modifications de la vue de carte sur l’onglet des résultats sur la droite. Vous pouvez également cliquer sur le bouton de modification sur CodePen afin de modifier le code par ce biais. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    
Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Ajouter un repère](./map-add-pin.md)
* [Ajouter une fenêtre contextuelle](map-add-popup.md)
    

