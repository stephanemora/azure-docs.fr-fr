---
title: Ajouter des contrôles de carte dans Azure Maps | Microsoft Docs
description: Comment ajouter un contrôle de zoom, un contrôle de tonalité, un contrôle de rotation et un sélecteur de styles à une carte dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eefa639ca5f13d23c3b2048436d739a01bd0dfa
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975616"
---
# <a name="add-map-controls-to-azure-maps"></a>Ajouter des contrôles de carte à Azure Maps

Cet article décrit comment ajouter des contrôles de carte à une carte. Vous apprenez aussi à créer une carte avec tous les contrôles et un [sélecteur de styles](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker).

## <a name="add-zoom-control"></a>Ajouter un contrôle de zoom

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Ajout d’un contrôle de zoom</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code définit la clé d’abonnement et crée un objet carte sans prédéfinir le style. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le contrôle de zoom vous permet d’agrandir et de réduire la carte. Le deuxième bloc de code crée un objet contrôle de zoom à l’aide de l’atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.controls.zoomcontrol) et l’ajoute à la carte à l’aide de la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte. Le contrôle de zoom se situe dans le **détecteur d’événements** de la carte pour s’assurer du chargement une fois que la carte est entièrement chargée.

## <a name="add-pitch-control"></a>Ajouter un contrôle de tonalité

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de tonalité' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Ajout d’un contrôle de tonalité</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code définit la clé d’abonnement et crée un objet carte sans prédéfinir le style. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le contrôle de tonalité vous permet de changer la tonalité de la carte. Le deuxième bloc de code crée un objet contrôle de tonalité à l’aide de l’atlas [PitchControl](/javascript/api/azure-maps-control/atlas.controls.pitchcontrol) et l’ajoute à la carte à l’aide de la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte. Le contrôle de tonalité se situe dans le **détecteur d’événements** de la carte pour s’assurer du chargement une fois que la carte est entièrement chargée.

## <a name="add-compass-control"></a>Ajouter un contrôle de boussole

<iframe height='500' scrolling='no' title='Ajout d’un contrôle de rotation' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Ajout d’un contrôle de rotation</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code définit la clé d’abonnement et crée un objet carte sans prédéfinir le style. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le deuxième bloc de code crée un objet contrôle de boussole à l’aide de l’atlas [CompassControl](/javascript/api/azure-maps-control/atlas.controls.compasscontrol). Il ajoute aussi le contrôle de boussole à la carte à l’aide de la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte. Le contrôle de boussole se situe dans le **détecteur d’événements** de la carte pour s’assurer du chargement une fois que la carte est entièrement chargée.

## <a name="a-map-with-all-controls"></a>Une carte avec tous les contrôles

<iframe height='500' scrolling='no' title='Une carte avec tous les contrôles' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la section <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Une carte avec tous les contrôles</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code définit la clé d’abonnement et crée un objet carte sans prédéfinir le style. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le deuxième bloc de code crée un objet contrôle de boussole à l’aide de l’atlas [CompassControl](/javascript/api/azure-maps-control/atlas.controls.compasscontrol) et l’ajoute à la carte à l’aide de la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

Le troisième bloc de code crée un objet contrôle de zoom à l’aide de l’atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.controls.zoomcontrol) et l’ajoute à la carte à l’aide de la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

Le quatrième bloc de code crée un objet contrôle de tonalité à l’aide de l’atlas [PitchControl](/javascript/api/azure-maps-control/atlas.controls.pitchcontrol) et l’ajoute à la carte à l’aide de la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

Le dernier bloc de code crée un objet sélecteur de style à l’aide de l’atlas [StyleControl](/javascript/api/azure-maps-control/atlas.controls.stylecontrol) et l’ajoute à la carte à l’aide de la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte. Tous les objets de contrôle sont ajoutés dans le **détecteur d’événements** de la carte pour assurer leur chargement une fois la carte complètement chargée.

L’ordre des objets de contrôle dans le script détermine l’ordre dans lequel ils apparaissent sur la carte. Pour modifier l’ordre des commandes sur la carte, vous pouvez changer leur ordre dans le script.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Pour voir des codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter un repère](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter une fenêtre contextuelle](./map-add-popup.md)