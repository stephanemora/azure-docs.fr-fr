---
title: Afficher le trafic avec Azure Maps | Microsoft Docs
description: Comment afficher les données de trafic sur une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/10/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 45f7913c5cc69f99b01ba1a911910273673856d3
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51513759"
---
# <a name="show-traffic-on-the-map"></a>Afficher le trafic sur la carte

Cet article explique comment afficher les informations relatives au trafic et aux incidents sur la carte.

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='456' scrolling='no' title='Afficher le trafic sur une carte' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Afficher le trafic sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](map-create.md) pour obtenir des instructions.

Le deuxième bloc de code utilise la fonction [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) dans la fonction [Détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) de la classe map pour afficher les flux de trafic et les incidents sur la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Pour voir des exemples de codes complets, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Page d’exemples de code](https://aka.ms/AzureMapsSamples)

Améliorez l’expérience de vos utilisateurs :

> [!div class="nextstepaction"]
> [Interaction avec la carte - Événements de souris](./map-events.md)

> [!div class="nextstepaction"]
> [Building an accessible map](./map-accessibility.md) (Création d’une carte accessible)
