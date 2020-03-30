---
title: Afficher le trafic sur une carte | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à afficher des données de trafic sur une carte à l’aide du SDK web Microsoft Azure Maps.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534812"
---
# <a name="show-traffic-on-the-map"></a>Afficher le trafic sur la carte

Deux types de données de trafic sont disponibles dans Azure Maps :

- Données d’incident : elles se composent de données de type point et ligne pour des éléments tels que les travaux, les fermetures de route et les accidents.
- Données de circulation : elles fournissent des métriques concernant la circulation sur les routes. Les données de circulation sont souvent utilisées pour colorer les routes. Les couleurs dépendent du volume de circulation qui entraîne un ralentissement par rapport à la limite de vitesse ou à une autre métrique. Les données de circulation dans Azure Maps ont trois métriques différentes de mesure :
    - `relative` : est relatif à la vitesse de circulation libre de la route.
    - `absolute` : est la vitesse absolue de tous les véhicules sur la route.
    - `relative-delay` : affiche les zones qui sont plus lentes que le délai moyen attendu.

Le code suivant montre comment afficher les données de trafic sur la carte.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus.

<br/>

<iframe height='500' scrolling='no' title='Afficher le trafic sur une carte' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Afficher le trafic sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Options de superposition du trafic

L’outil suivant vous permet de basculer entre les différents paramètres de superposition du trafic pour voir comment le rendu change. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Options de superposition du trafic" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Traffic overlay options</a> (Options de superposition du trafic) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Améliorez l’expérience de vos utilisateurs :

> [!div class="nextstepaction"]
> [Interaction avec la carte - Événements de souris](map-events.md)

> [!div class="nextstepaction"]
> [Building an accessible map](map-accessibility.md) (Création d’une carte accessible)

> [!div class="nextstepaction"]
> [Page d’exemples de code](https://aka.ms/AzureMapsSamples)
