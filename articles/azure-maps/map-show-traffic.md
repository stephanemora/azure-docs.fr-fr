---
title: Afficher le trafic avec Azure Maps | Microsoft Docs
description: Comment afficher les données de trafic sur une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600117"
---
# <a name="show-traffic-on-the-map"></a>Afficher le trafic sur la carte

Cet article explique comment afficher les informations relatives au trafic et aux incidents sur la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='456' scrolling='no' title='Afficher le trafic sur une carte' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Afficher le trafic sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consultez [Create a map](map-create.md) (Créer une carte) pour obtenir des instructions.

Le deuxième bloc de code utilise la fonction [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) de la classe map pour afficher les flux de trafic et les incidents sur la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Map class](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) (Classe Map)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) (setTraffic)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Interaction avec la carte - Événements de souris](./map-events.md)
* [Building an accessible map](./map-accessibility.md) (Création d’une carte accessible)

Consultez notre [page d’exemples de code](http://aka.ms/AzureMapsSamples) pour d’autres scénarios de mappage.
