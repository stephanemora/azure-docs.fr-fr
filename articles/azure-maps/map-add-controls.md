---
title: Ajouter des contrôles de carte dans Azure Maps | Microsoft Docs
description: Comment ajouter un contrôle de zoom, un contrôle de tonalité, un contrôle de rotation et un sélecteur de styles à une carte dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 08/29/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 435c6545b69b4457c3e1035fb8125399d4c9c23a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666129"
---
# <a name="add-map-controls-to-azure-maps"></a>Ajouter des contrôles de carte à Azure Maps

Cet article décrit comment ajouter des contrôles de carte à une carte. Vous apprenez aussi à créer une carte avec tous les contrôles et un [sélecteur de styles](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker).

## <a name="add-zoom-control"></a>Ajouter un contrôle de zoom

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Ajout d’un contrôle de zoom</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code dans le code ci-dessus crée un objet carte. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le deuxième bloc de code crée un objet contrôle de zoom à l’aide de l’atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest).

Le contrôle de zoom vous permet d’agrandir et de réduire la carte. Le troisième bloc ajoute le contrôle de zoom à la carte à l’aide de la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

## <a name="add-pitch-control"></a>Ajouter un contrôle de tonalité

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de tonalité' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Ajout d’un contrôle de tonalité</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus crée un objet carte avec le style défini sur Nuances de gris. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le deuxième bloc de code crée un objet contrôle de tonalité à l’aide de l’atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest).

Le contrôle de tonalité vous permet de changer la tonalité de la carte. Le troisième bloc ajoute le contrôle de tonalité à la carte à l’aide de la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

## <a name="add-compass-control"></a>Ajouter un contrôle de boussole

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de rotation' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Ajout d’un contrôle de rotation</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code dans le code ci-dessus crée un objet carte. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le deuxième bloc de code crée un objet contrôle de boussole à l’aide de l’atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol). Il ajoute aussi le contrôle de boussole à la carte à l’aide de la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

## <a name="a-map-with-all-controls"></a>Une carte avec tous les contrôles

<iframe height='500' scrolling='no' title='Une carte avec tous les contrôles' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Une carte avec tous les contrôles</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code dans le code ci-dessus crée un objet carte. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le deuxième bloc de code crée un objet contrôle de boussole à l’aide de l’atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) et l’ajoute à la carte à l’aide de la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

Le troisième bloc de code crée un objet contrôle de zoom à l’aide de l’atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) et l’ajoute à la carte à l’aide de la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

Le quatrième bloc de code crée un objet contrôle de tonalité à l’aide de l’atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) et l’ajoute à la carte à l’aide de la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

Le dernier bloc de code ajoute un objet sélecteur de styles à la carte à l’aide de l’atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) et l’ajoute à la carte à l’aide de la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol)
    * [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest)
    * [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Ajouter un repère](./map-add-pin.md)
* [Ajouter une fenêtre contextuelle](./map-add-popup.md)