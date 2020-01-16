---
title: Récupérer des données de forme à partir du gestionnaire de dessins dans Azure Maps | Microsoft Docs
description: Obtention de données de forme à l’aide du kit de développement logiciel (SDK) web Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bc8efe46453f76d353842b32bd52b41838979b49
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432821"
---
# <a name="get-shape-data"></a>Obtenir des données de forme

Cet article explique comment obtenir des données pour des formes qui ont été dessinées sur la carte à l’aide de la fonction **drawingManager.getSource()** du [gestionnaire de dessins](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Il peut y avoir différents scénarios dans lesquels vous pouvez souhaiter extraire les données GeoJSON d’une forme dessinée et les utiliser ailleurs.  


## <a name="get-data-from-drawn-shape"></a>Obtenir des données à partir d’une forme dessinée

La fonction suivante obtient les données source de la forme dessinée et les affiche à l’écran. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet, dans lequel vous pouvez dessiner une forme pour tester les fonctionnalités :

<br/>

<iframe height="686" title="Obtenir des données de forme" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consultez le stylet <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get shape data</a> (Obtenir des données de forme) Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser les fonctionnalités supplémentaires du module Outils de dessin :

> [!div class="nextstepaction"]
> [Réagir aux événements de dessin](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Types d’interaction et raccourcis clavier](drawing-tools-interactions-keyboard-shortcuts.md)

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestionnaire de dessins](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barre d’outils de dessin](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
