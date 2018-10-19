---
title: Ajouter un repère avec Azure Maps | Microsoft Docs
description: Guide d’ajout d’un repère à une carte JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 35b22e28a6a339af6f6f6e8db0655f2ae6de0118
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122168"
---
# <a name="add-pins-to-the-map"></a>Ajouter des repères à une carte

Cet article vous expliquez comment ajouter un repère à une carte.  

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Ajouter un repère à une carte' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Add a pin to a map</a> (Ajouter un repère à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un repère est créé et ajouté à la carte. Un repère est une [Fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest), [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) étant sa propriété Fonctionnalité. Utilisez `new atlas.data.Feature(new atlas.data.Point())` pour créer un repère et définir ses propriétés. Une couche de repères est un tableau de repères. Utilisez la fonction [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) de la classe map pour ajouter une couche de repères à la carte et définir les propriétés de la couche de repères. Pour définir les propriétés d’une couche de repères, accédez à [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une fenêtre contextuelle](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Ajouter une forme](./map-add-shape.md)