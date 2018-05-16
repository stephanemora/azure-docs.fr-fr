---
title: Afficher le trafic avec Azure Maps | Microsoft Docs
description: Comment afficher les données de trafic sur une carte Javascript
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
ms.openlocfilehash: a90304de1fbdb4550d0789aa71b66eebece8a02e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="show-traffic-on-the-map"></a>Afficher le trafic sur la carte

Cet article explique comment afficher les informations relatives au trafic et aux incidents sur la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='456' scrolling='no' title='Afficher le trafic sur une carte' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Afficher le trafic sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consultez [Create a map](map-create.md) (Créer une carte) pour obtenir des instructions.

Le deuxième bloc de code utilise la fonction [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) de la classe map pour afficher les flux de trafic et les incidents sur la carte.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Map class](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) (Classe Map)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) (setTraffic)
