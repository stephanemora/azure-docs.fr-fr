---
title: Ajouter une couche de symboles dans Azure Maps | Microsoft Docs
description: Découvrez comment ajouter des symboles à la carte Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2580f1177bf9e6e3a92934f88a5d8ab51894e8d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771666"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Ajouter une couche de symboles à une carte

Cet article vous montre comment afficher des données de point sur une carte, à partir d’une source de données telle qu’une couche de symboles. Les couches de symboles sont affichées à l’aide de WebGL. Elles prennent en charge des ensembles de points beaucoup plus grands que les marqueurs HTML, mais elles ne prennent pas en charge les éléments de style CSS et HTML traditionnels.  

> [!TIP]
> Les couches de symboles par défaut affichent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche afin qu’elle n’affiche que les fonctionnalités de géométrie de point, définissez la propriété `filter` de la couche sur `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` si vous voulez également inclure les fonctionnalités MultiPoint.

## <a name="add-a-symbol-layer"></a>Ajouter un calque de symboles

<iframe height='500' scrolling='no' title='Switch pin location' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a> (Changer l’emplacement du repère) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code ci-dessus construit un objet carte. Vous pouvez consulter la section [Créer une carte](./map-create.md) pour obtenir des instructions.

Dans le deuxième bloc de code, un objet source de données est créé à l’aide de la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un élément [Feature] contenant une géométrie [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) est wrappé par la classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) pour faciliter sa mise à jour, puis est créé et ajouté à la source de données.

Le troisième bloc de code crée un [écouteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) et met à jour les coordonnées du point à chaque clic de souris, par l’intermédiaire de la méthode [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) de la classe shape.

Une [couche de symboles](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utilise du texte ou des icônes pour afficher les données basées sur le point, qui sont wrappées dans la [source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en tant que symboles sur la carte.  La source de données, le détecteur d’événements Click et la couche de symboles sont créés et ajoutés à la carte dans la fonction de [détecteur d’événements](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) `ready` pour garantir que le point s’affiche une fois la carte chargée et accessible.

> [!TIP]
> Par défaut, pour améliorer les performances, les couches de symboles optimisent le rendu des symboles en masquant les symboles qui se chevauchent. Quand vous effectuez un zoom avant, les symboles masqués deviennent visibles. Pour désactiver cette fonctionnalité et afficher tous les symboles en permanence, définissez la propriété `allowOverlap` des options `iconOptions` sur `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Ajouter une icône personnalisée à une couche de symboles

Les couches de symboles sont affichées à l’aide de WebGL. Toutes les ressources, telles que les images d’icône, doivent donc être chargées dans le contexte de WebGL. Cet exemple montre comment ajouter une icône personnalisée dans les ressources de la carte, puis l’utiliser pour afficher les données de point avec un symbole personnalisé sur la carte. La propriété `textField` de la couche de symboles nécessite une expression. Dans le cas présent, nous voulons afficher la propriété de température, mais étant donné qu’il s’agit d’un nombre, elle doit être convertie en chaîne. De plus, nous voulons lui ajouter « °F ». Pour ce faire, une expression peut être utilisée : `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Icône de l’image de symbole personnalisée' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Personnaliser une couche de symboles 

Il y a de nombreuses options de style disponibles pour la couche de symboles. Voici un outil permettant de les tester.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de symboles' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Ajouter une fenêtre contextuelle](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une forme](map-add-shape.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Ajouter des marqueurs HTML](map-add-bubble-layer.md)
