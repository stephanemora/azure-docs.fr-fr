---
title: Ajouter une barre d’outils de dessin à une carte | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à ajouter une barre d’outils de dessin à une carte à l’aide du Kit de développement logiciel (SDK) web Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fd235f3f39d67f86c8387add79ca0dbf17dc5906
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911679"
---
# <a name="drawing-tool-events"></a>Événements des outils de dessin

Il est souvent utile de réagir à certains événements lorsque l’utilisateur utilise les outils de dessin sur la carte. Le tableau suivant présente tous les événements pris en charge par la classe `DrawingManager`.

| Événement | Description |
|-------|-------------|
| `drawingchanged` | Se déclenche en cas d’ajout ou de modification d’une coordonnée dans une forme. | 
| `drawingchanging` | Se déclenche lorsqu’une coordonnée d’aperçu d’une forme est affichée. Par exemple, se déclenche plusieurs fois en cas de glisser-déposer d’une coordonnée. | 
| `drawingcomplete` | Se déclenche lorsqu’une forme vient d’être dessinée ou sort du mode d’édition. |
| `drawingmodechanged` | Se déclenche lorsque le mode de dessin change. Le nouveau mode de dessin est passé dans le gestionnaire d’événements. |
| `drawingstarted` | Se déclenche lorsque l’utilisateur commence à dessiner une forme ou en met une en mode d’édition.  |

Le code suivant illustre le fonctionnement des événements du module Outils de dessin. Dessinez des formes sur la carte et regardez les événements se déclencher.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Événements des outils de dessin" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>Événements des outils de dessin</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exemples

Voici quelques exemples de scénarios courants qui utilisent les événements des outils de dessin.

### <a name="select-points-in-polygon-area"></a>Sélectionner des points dans une zone polygonale

Le code suivant montre comment surveiller le dessin de formes représentant des zones polygonales (polygones, rectangles et cercles) et identifier les points de données de la carte qui se trouvent dans la zone dessinée. L’événement `drawingcomplete` est utilisé pour déclencher la logique de sélection. La logique de sélection boucle sur tous les points de données de la carte et teste la présence d’une intersection avec la zone polygonale de la forme dessinée. Cet exemple utilise la bibliothèque open source [Turf.js](https://turfjs.org/) pour effectuer un calcul d’intersection spatiale.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sélectionner des données dans la zone polygonale dessinée" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Sélectionner des données dans une zone polygonale dessinée</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Dessiner une zone polygonale et effectuer une recherche

Le code suivant montre comment effectuer une recherche de points d’intérêt à l’intérieur d’une zone de forme une fois que l’utilisateur a terminé de dessiner la forme. L’événement `drawingcomplete` est utilisé pour déclencher la logique de recherche. Si l’utilisateur dessine un rectangle ou un polygone, une recherche est effectuée dans la forme géométrique. Si un cercle est dessiné, le rayon et la position du centre sont utilisés pour effectuer une recherche de point d’intérêt. L’événement `drawingmodechanged` est utilisé pour déterminer quand l’utilisateur bascule en mode de dessin, et efface la zone de dessin.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dessiner une zone polygonale et effectuer une recherche" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Dessiner et effectuer une recherche dans une zone polygonale dessinée</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Créer un outil de mesure

Le code suivant montre comment utiliser les événements de dessin pour créer un outil de mesure. `drawingchanging` permet de surveiller la forme en cours de dessin. Lorsque l’utilisateur déplace la souris, les dimensions de la forme sont calculées. L’événement `drawingcomplete` permet d’effectuer un calcul final sur la forme une fois dessinée. L’événement `drawingmodechanged` est utilisé pour déterminer quand l’utilisateur bascule en mode de dessin, et efface la zone de dessin et les anciennes informations de mesure.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Outil de mesure" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Outil de mesure</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser les fonctionnalités supplémentaires du module Outils de dessin :

> [!div class="nextstepaction"]
> [Récupérer les données de la forme](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Types d’interaction et raccourcis clavier](drawing-tools-interactions-keyboard-shortcuts.md)

Découvrez le module Service :

> [!div class="nextstepaction"]
> [Module de services](how-to-use-services-module.md)

Découvrez d’autres exemples de code :

> [!div class="nextstepaction"]
> [Page d’exemples de code](https://aka.ms/AzureMapsSamples)
