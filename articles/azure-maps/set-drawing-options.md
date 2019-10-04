---
title: Définir les options de dessin dans Azure Maps | Microsoft Docs
description: Comment définir des options de dessin à l’aide du kit de développement logiciel (SDK) web Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309719"
---
# <a name="set-drawing-options"></a>Définir les options de dessin

Cet article explique comment différentes options du [gestionnaire de dessins](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) modifient l’expérience utilisateur. Vous pouvez spécifier des options pour le gestionnaire de dessins lors de l’instanciation ou utiliser la fonction **drawingManager.setOptions()** pour définir des options.


## <a name="set-drawing-mode"></a>Définir le mode dessin

Le code suivant crée une instance du gestionnaire de dessins et définit l’option de mode **dessin**. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Le code ci-dessous est un exemple complet d’exécution de la procédure de définition d’un mode dessin du gestionnaire de dessins. Cliquez sur la carte pour commencer à dessiner un polygone.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dessiner un polygone" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Draw a polygon</a> (Dessiner un polygone) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Définir le type d’interaction

Le code suivant définit le type d’interaction de dessin auquel le gestionnaire de dessins doit adhérer. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Voici un exemple de code qui implémente la fonctionnalité qui vous permet de dessiner librement sur la carte, en maintenant le bouton gauche de la souris enfoncé et en faisant glisser. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dessin libre" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a> (Dessin libre) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="customizing-drawing-options"></a>Personnalisation des options de dessin

Les exemples précédents ont montré comment personnaliser les options de dessin lors de l’instanciation du gestionnaire de dessins. Vous pouvez également définir les options du gestionnaire de dessins à l’aide de la fonction **drawingManager.setOptions()** . Vous trouverez ci-dessous un outil permettant de tester la personnalisation de toutes les options du gestionnaire de dessins à l’aide de la fonction setOptions.

<br/>

<iframe height="685" title="Personnaliser le gestionnaire de dessins" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get shape data</a> (Obtenir des données de forme) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestionnaire de dessins](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barre d’outils de dessin](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
