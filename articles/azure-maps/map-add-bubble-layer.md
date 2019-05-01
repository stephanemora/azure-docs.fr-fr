---
title: Ajouter une couche de bulles dans Azure Maps | Microsoft Docs
description: Comment ajouter une couche de bulles à une carte Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769683"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Ajouter une couche de bulles à une carte

Cet article vous montre comment vous pouvez afficher des données de point sur une carte, à partir d’une source de données telle qu’une couche de bulles. Les couches de bulles affichent sur la carte des points sous la forme de cercles, avec un rayon de pixels fixe. 

> [!TIP]
> Les couches de bulles par défaut affichent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche, telle qu’elle s’affiche uniquement des point geometry fonctionnalités ensemble la `filter` propriété de la couche pour `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` si vous souhaitez inclure également les fonctionnalités MultiPoint.

## <a name="add-a-bubble-layer"></a>Ajouter un calque de bulles

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> (Source de données de la couche de bulles) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dans le code ci-dessus, le premier bloc de code construit un objet de carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un tableau d’objets [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) est défini puis ajouté à l’objet [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest).

Une [couche de bulles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) affiche les données basées sur les points, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que cercles sur la carte. Le dernier bloc de code crée une couche de bulles et l’ajoute à la carte. Consultez les propriétés d’une couche de bulles dans [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Le tableau d’objets Point, la source de données et la couche de bulles sont créés et ajoutés à la carte dans la fonction [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events), pour garantir que le cercle s’affichera après le chargement complet de la carte.

## <a name="show-labels-with-a-bubble-layer"></a>Afficher les étiquettes avec une couche de bulles

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> (Source de données multicouche) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le code ci-dessus montre comment visualiser et étiqueter des données sur la carte. Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Le deuxième bloc de code crée un objet [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Il crée ensuite un objet source de données à l’aide de la classe [data source](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest), puis ajoute le point à la source de données.

Une [couche de bulles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) affiche les données basées sur les points, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que cercles sur la carte. Le troisième bloc de code crée une couche de bulles et l’ajoute à la carte. Consultez les propriétés d’une couche de bulles dans [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte. Le dernier bloc de code crée et ajoute une couche de symboles à la carte qui affiche l’étiquette de texte de la bulle. Consultez les propriétés d’une couche de symboles à la page [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

La source de données et les couches sont créées et ajoutées à la carte dans la fonction [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events), pour garantir que les données s’afficheront après le chargement complet de la carte.

## <a name="customize-a-bubble-layer"></a>Personnaliser une couche de bulles

La couche de bulles ne dispose que de quelques options de style. Voici un outil qui va vous permettre de les essayer.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de bulles' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Bubble Layer Options</a> (Options de la couche de bulles) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](map-add-pin.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)