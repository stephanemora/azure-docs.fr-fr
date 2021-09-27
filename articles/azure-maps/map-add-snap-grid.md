---
title: Ajouter une grille d'alignement à la carte | Microsoft Azure Maps
description: Apprenez à ajouter une grille d'alignement à une carte à l'aide du kit de développement logiciel (SDK) web Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 3202461de662e0f789b6c3a6187dcfbe2fa58764
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439611"
---
# <a name="add-a-snap-grid-to-the-map"></a>Ajouter une grille d'alignement à la carte

Une grille d'alignement permet de tracer plus facilement des formes avec des bords et des nœuds partagés, et des lignes plus droites. L'alignement des formes sur une grille permet de tracer les contours des constructions ou les voies d'un réseau sur la carte.

La résolution de la grille d'alignement est exprimée en pixels. La grille est carrée et proportionnelle au niveau de zoom de l'entier le plus proche. À chaque niveau de zoom, la grille est mise à l'échelle selon un facteur de deux par rapport à la surface physique du monde réel.

## <a name="use-a-snap-grid"></a>Utiliser une grille d'alignement

Créez une grille d'alignement à l'aide de la classe `atlas.drawing.SnapGridManager` et transmettez une référence à la carte à laquelle vous souhaitez connecter le gestionnaire. Définissez l'option `showGrid` sur `true` pour afficher la grille. Pour aligner une forme sur la grille, transmettez-la à la fonction `snapShape` des gestionnaires de grille d'alignement. Si vous souhaitez aligner un groupe de positions, transmettez-le à la fonction `snapPositions`.

L'exemple suivant permet d'aligner un marqueur HTML sur une grille. Les outils de dessin permettent d'aligner des formes dessinées sur la grille lorsque l'événement `drawingcomplete` se déclenche.

<br/>

<iframe height="500" scrolling="no" title="Utiliser une grille d'alignement" src="https://codepen.io/azuremaps/embed/rNmzvXO?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consultez l'extrait <a href="https://codepen.io/azuremaps/pen/rNmzvXO">Utiliser une carte d'alignement</a> d'Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) sur <a href="https://codepen.io">CodePen</a>.
</iframe>


## <a name="snap-grid-options"></a>Options de grille d'alignement

L'exemple suivant illustre les différentes options de personnalisation disponibles pour le gestionnaire de grille d'alignement. Les styles de ligne de la grille peuvent être personnalisés en extrayant la couche de lignes sous-jacente à l'aide de la fonction `getGridLayer` des gestionnaires de grille d'alignement.

<br/>

<iframe height="700" scrolling="no" title="Options de grille d'alignement" src="https://codepen.io/azuremaps/embed/RwVZJry?default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consultez l'extrait <a href="https://codepen.io/azuremaps/pen/RwVZJry">Options de ligne d'alignement</a> d'Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) sur <a href="https://codepen.io">CodePen</a>.
</iframe>


## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser d'autres fonctionnalités du module Outils de dessin :

> [!div class="nextstepaction"]
> [Obtenir des données de forme](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Réagir aux événements de dessin](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Types d’interaction et raccourcis clavier](drawing-tools-interactions-keyboard-shortcuts.md)