---
title: Ajouter un repère avec Azure Maps | Microsoft Docs
description: Guide d’ajout d’un repère à une carte JavaScript
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
ms.openlocfilehash: 189b30ff0b5c47d0a4bd3181c8eb2143213ad6cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="add-pins-to-the-map"></a>Ajouter des repères à une carte

Cet article vous expliquez comment ajouter un repère à une carte.  

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Ajouter un repère à une carte' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Add a pin to a map</a> (Ajouter un repère à une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un repère est créé et ajouté à la carte. Un repère est une [Fonctionnalité](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de [Point](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest), [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest) étant sa propriété Fonctionnalité. Utilisez `new atlas.data.Feature(new atlas.data.Point())` pour créer un repère et définir ses propriétés. Une couche de repères est un tableau de repères. Utilisez la fonction [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) de la classe map pour ajouter une couche de repères à la carte et définir les propriétés de la couche de repères. Pour définir les propriétés d’une couche de repères, accédez à [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
