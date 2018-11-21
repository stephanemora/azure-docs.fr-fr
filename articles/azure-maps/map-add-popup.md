---
title: Ajouter une fenêtre contextuelle avec Azure Maps | Microsoft Docs
description: Guide d’ajout d’une fenêtre contextuelle à une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568112"
---
# <a name="add-a-popup-to-the-map"></a>Ajouter une fenêtre contextuelle à la carte

Cet article vous explique comment ajouter une fenêtre contextuelle à un point sur une carte.

## <a name="understand-the-code"></a>Comprendre le code

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Ajouter une fenêtre contextuelle à l’aide d’Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Affichez l’objet Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Ajouter une fenêtre contextuelle à l’aide d’Azure Maps</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions. Il crée également du contenu HTML à afficher dans la fenêtre contextuelle.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un point est une [Fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de la classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Un objet Point doté d’un nom et d’une description de propriétés est ensuite créé et ajouté à la source de données.

Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte.  Une couche de symbole est créée dans le troisième bloc de code. La source de données est ajoutée à la couche de symbole, qui est ensuite ajoutée à la carte.

Le quatrième bloc de code crée un [objet de fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Les propriétés de la fenêtre contextuelle, telles que la position et pixelOffset, font partie de la section [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). La section PopupOptions peut être définie dans le constructeur de la fenêtre contextuelle ou via la fonction [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) de la classe Popup. Un écouteur d’événements `mouseover` est ensuite créé pour la couche de symbole.

Le dernier bloc de code crée une fonction qui est déclenchée par l’écouteur d’événements `mouseover`. Il définit le contenu et les propriétés de la fenêtre contextuelle et ajoute l’objet de fenêtre contextuelle à la carte.

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
