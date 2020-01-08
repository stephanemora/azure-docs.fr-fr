---
title: Choisir un style de carte dans Azure Maps
titleSuffix: Azure Maps
description: Apprenez-en davantage sur les fonctionnalités liées au style dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e1d3d1b5904eded9f35c5ba628bea6426e7afaf0
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531545"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Choisir un style de carte dans Azure Maps

Un grand nombre des [styles de carte pris en charge dans Azure Maps](./supported-map-styles.md) sont disponibles dans le SDK web. Cet article explique comment utiliser les fonctionnalités liées au style pour définir un style lors du chargement de carte, définir un nouveau style et utiliser le contrôle du sélecteur de styles.

## <a name="set-style-on-map-load"></a>Définir un style lors du chargement de carte

Dans le code suivant, l’option `style` de la carte est définie sur `grayscale_dark` au moment de l’initialisation.

<br/>

<iframe height='500' scrolling='no' title='Définition du style lors du chargement de carte' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Définition du style lors du chargement de carte</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Mettre à jour le style

Dans le code suivant, après le chargement d’une instance de carte, le style de carte est mis à jour de `road` en `satellite` à l’aide de la fonction [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de la carte.

<br/>

<iframe height='500' scrolling='no' title='Mise à jour du style' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Mise à jour du style</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Ajouter le sélecteur de styles

Le code suivant ajoute un [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) à la carte afin que l’utilisateur puisse facilement basculer entre les différents styles de carte. 

<br/>

<iframe height='500' scrolling='no' title='Ajout du sélecteur de styles' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Ajout du sélecteur de styles</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Par défaut, le contrôle de sélecteur de styles liste tous les styles disponibles quand vous utilisez le niveau tarifaire S0 par défaut d’Azure Maps. Si vous voulez réduire le nombre de styles figurant dans cette liste, passez dans l’option `mapStyle` du sélecteur de styles un tableau des styles que vous voulez voir apparaître dans la liste. Si vous utilisez S1 et que vous voulez afficher tous les styles disponibles, affectez à l’option `mapStyles` du sélecteur de styles la valeur `"all"`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Ajouter des contrôle à vos cartes :

> [!div class="nextstepaction"]
> [Ajouter des contrôles de carte](map-add-controls.md)

> [!div class="nextstepaction"]
> [Ajouter un repère](map-add-pin.md)
