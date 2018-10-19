---
title: Ajouter une fenêtre contextuelle avec Azure Maps | Microsoft Docs
description: Guide d’ajout d’une fenêtre contextuelle à une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 76a7e230491d5e524a1d73437a56d12594cfebe2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127435"
---
# <a name="add-a-popup-to-the-map"></a>Ajouter une fenêtre contextuelle à la carte

Cet article vous explique comment ajouter une fenêtre contextuelle à une carte.  

## <a name="understand-the-code"></a>Comprendre le code

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Ajouter une fenêtre contextuelle à une carte' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>Add a popup to a map</a> (Ajouter une fenêtre contextuelle à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un repère et l’ajoute à la carte. Pour plus d’instructions, consultez la section relative à [l’ajout d’un repère à la carte](./map-add-pin.md).

Le troisième bloc de code crée le contenu à afficher dans une fenêtre contextuelle. Le contenu de la fenêtre contextuelle est un élément HTML.

Le quatrième bloc de code crée un [objet de fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Les propriétés de la fenêtre contextuelle, telles que le contenu et la position, font partie des [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). Les PopupOptions peuvent être définies dans le constructeur de la fenêtre contextuelle ou via la fonction [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) de la classe popup.

Le dernier bloc de code utilise la fonction [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) de la classe map pour écouter l’événement mouseover sur les repères et utilise la fonction [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) de la classe popup pour ouvrir la fenêtre contextuelle si l’événement se produit.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants qui sont très intéressants :

> [!div class="nextstepaction"]
> [Ajouter une forme](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Ajouter du code HTML personnalisé](./map-add-custom-html.md)
