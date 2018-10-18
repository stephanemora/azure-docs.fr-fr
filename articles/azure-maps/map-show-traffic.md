---
title: Afficher le trafic avec Azure Maps | Microsoft Docs
description: Comment afficher les données de trafic sur une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6d5c721ab84c28bae9415dceeaa09fd12cc05824
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733012"
---
# <a name="show-traffic-on-the-map"></a>Afficher le trafic sur la carte

Cet article explique comment afficher les informations relatives au trafic et aux incidents sur la carte.

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='456' scrolling='no' title='Afficher le trafic sur une carte' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Afficher le trafic sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](map-create.md) pour obtenir des instructions.

Le deuxième bloc de code utilise la fonction [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) de la classe map pour afficher les flux de trafic et les incidents sur la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Page d’exemples de code](http://aka.ms/AzureMapsSamples)

Améliorez l’expérience de vos utilisateurs :

> [!div class="nextstepaction"]
> [Interaction avec la carte - Événements de souris](./map-events.md)

> [!div class="nextstepaction"]
> [Building an accessible map](./map-accessibility.md) (Création d’une carte accessible)
