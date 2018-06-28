---
title: Affichage de directions avec Azure Maps | Microsoft Docs
description: Comment afficher des directions entre deux emplacements sur une carte Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "35772011"
---
# <a name="show-directions-from-a-to-b"></a>Afficher des directions de A à B 

Cet article vous explique comment exécuter une requête d’itinéraire et afficher l’itinéraire sur la carte. 

## <a name="understand-the-code"></a>Comprendre le code

<iframe height='500' scrolling='no' title='Afficher des directions de A à B sur une carte' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Afficher des directions de A à B sur une carte) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consultez la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée et ajoute des repères sur la carte pour représenter le point de départ et le point d’arrivée de l’itinéraire. Pour plus d’instructions, consultez la section relative à [l’ajout d’un repère sur la carte](map-add-pin.md).

Le troisième bloc de code utilise la fonction [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) de la classe map pour définir le rectangle englobant de la carte en fonction des points de départ et d’arrivée de l’itinéraire.

Le quatrième bloc de code envoie un élément [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [l’API d’itinéraire Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Le dernier bloc de code analyse la réponse entrante. Si la réponse est correcte, il collecte les données de latitude et de longitude pour chaque étape. Il crée un ensemble de lignes en reliant chaque étape à l’étape suivante. Il ajoute toutes ces lignes sur la carte pour restituer l’itinéraire. Pour plus d’instructions, consultez la section relative à [l’ajout d’une ligne sur la carte](./map-add-shape.md#addALine).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants : 
* [Afficher le trafic sur la carte](./map-show-traffic.md)
* [Interaction avec la carte - Événements de souris](./map-events.md)
