---
title: Ajouter une fenêtre contextuelle avec Azure Maps | Microsoft Docs
description: Guide d’ajout d’une fenêtre contextuelle à une carte JavaScript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: 7425081597bfa9379594597277555ee30809c4e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-popup-to-the-map"></a>Ajouter une fenêtre contextuelle à la carte

Cet article vous explique comment ajouter une fenêtre contextuelle à une carte.  

## <a name="understand-the-code"></a>Comprendre le code

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Ajouter une fenêtre contextuelle à une carte' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>Add a popup to a map</a> (Ajouter une fenêtre contextuelle à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consultez la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un repère et l’ajoute à la carte. Pour plus d’instructions, consultez la section relative à [l’ajout d’un repère à la carte](./map-add-pin.md).

Le troisième bloc de code crée le contenu à afficher dans une fenêtre contextuelle. Le contenu de la fenêtre contextuelle est un élément HTML. 

Le quatrième bloc de code crée un [objet de fenêtre contextuelle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Les propriétés de la fenêtre contextuelle, telles que le contenu et la position, font partie des [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popupoptions?view=azure-iot-typescript-latest). Les PopupOptions peuvent être définies dans le constructeur de la fenêtre contextuelle ou via la fonction [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) de la classe popup.

Le dernier bloc de code utilise la fonction [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener) de la classe map pour écouter l’événement mouseover sur les repères et utilise la fonction [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open) de la classe popup pour ouvrir la fenêtre contextuelle si l’événement se produit.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
