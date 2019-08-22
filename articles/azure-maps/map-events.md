---
title: Gérer les événements de souris avec Azure Maps | Microsoft Docs
description: Guide pratique pour créer une carte interactive du SDK web avec des événements de carte
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976543"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagir avec la carte - Événements de souris

Cet article vous montre comment utiliser la propriété [events de la classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) pour mettre en évidence des événements sur la carte et sur différentes couches de la carte. Il montre également comment utiliser cette propriété pour mettre en évidence des événements lorsque vous interagissez avec un marqueur HTML.

## <a name="interact-with-the-map"></a>Interagir avec la carte

Manipulez la carte ci-dessous et observez les événements de souris correspondants mis en surbrillance sur la droite. Cliquez sur l’**onglet JS** pour afficher et modifier le code JavaScript. Vous pouvez également cliquer sur le bouton de **modification sur CodePen** afin de modifier le code par ce biais.

<br/>

<iframe height='600' scrolling='no' title='Interaction avec la carte - Événements de souris' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact with the map – mouse events (Interagir avec la carte - Événements de souris)</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagir avec les couches de la carte

Le code suivant met en évidence le nom des événements qui sont déclenchés quand vous interagissez avec la couche de symboles. Les couches de symboles, de bulles, de lignes et de polygones prennent toutes en charge le même ensemble d’événements. Les couches de carte thermique et de mosaïques ne prennent en charge aucun de ces événements.

<br/>

<iframe height='600' scrolling='no' title='Interaction avec la carte - Événements de couches' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interacting with the map – Layer Events</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagir avec un marqueur HTML

Le code suivant ajoute des événements de carte JavaScript à un marqueur HTML. Il met également en surbrillance le nom des événements qui sont déclenchés quand vous interagissez avec le marqueur HTML.

<br/>

<iframe height='500' scrolling='no' title='Interaction avec la carte - Événements de marqueurs HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting with the map - HTML Marker events</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Utiliser le module de service Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-maps)
