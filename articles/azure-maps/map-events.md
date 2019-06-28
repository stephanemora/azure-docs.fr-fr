---
title: Gérer les événements de souris avec Azure Maps | Microsoft Docs
description: Guide pratique pour créer une carte JavaScript interactive avec les événements de carte
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770446"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagir avec la carte - Événements de souris

Cet article vous montre comment utiliser la propriété [events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de la [classe map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) pour mettre en évidence des événements sur une carte et sur différentes couches de la carte. Il montre également comment utiliser cette propriété pour mettre en évidence des événements lorsque vous interagissez avec un marqueur HTML.

## <a name="interact-with-the-map"></a>Interagir avec la carte

<iframe height='600' scrolling='no' title='Interaction avec la carte - Événements de souris' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact with the map – mouse events (Interagir avec la carte - Événements de souris)</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Manipulez la carte ci-dessus et observez les événements de souris correspondants mis en surbrillance sur la droite. Cliquez sur l’**onglet JS** pour afficher et modifier le code JavaScript. Vous pouvez également cliquer sur le bouton de **modification sur CodePen** afin de modifier le code par ce biais.

## <a name="interact-with-map-layers"></a>Interagir avec les couches de la carte

<iframe height='600' scrolling='no' title='Interaction avec la carte - Événements de couches' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interacting with the map – Layer Events</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le code ci-dessus met en surbrillance le nom des événements qui sont déclenchés quand vous interagissez avec la couche de symboles. Les couches de symboles, de bulles, de lignes et de polygones prennent toutes en charge le même jeu d’événements. La couche de mosaïques ne prend pas en charge ces événements.

## <a name="interact-with-html-marker"></a>Interagir avec un marqueur HTML

<iframe height='500' scrolling='no' title='Interaction avec la carte - Événements de marqueurs HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting with the map - HTML Marker events</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le code ci-dessus ajoute des événements de carte Javascript à un marqueur HTML. Il met également en surbrillance le nom des événements qui sont déclenchés quand vous interagissez avec le marqueur HTML.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Utiliser le module de service Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Page d’exemples de code](https://aka.ms/AzureMapsSamples)
