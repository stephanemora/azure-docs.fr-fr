---
title: Ajouter une couche de symboles à une carte | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir comment utiliser la couche de symboles pour personnaliser et ajouter des symboles sur une carte à l’aide du SDK web Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 744d5ecd3aab02071f7c3aaff7dd760fc14a2a62
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911160"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Ajouter une couche de symboles à une carte

Un symbole peut être connecté à une source de données et utilisé pour restituer une icône et/ou un texte à un point donné. Les couches de symboles sont rendues à l’aide de WebGL et peuvent être utilisées pour afficher de grandes collections de points sur la carte. Cette couche peut afficher beaucoup plus de données de point sur la carte, avec de bonnes performances, qu’à l’aide de marqueurs HTML. Toutefois, la couche de symboles ne prend pas en charge les éléments CSS et HTML traditionnels pour le style.  

> [!TIP]
> Les couches de symboles par défaut affichent les coordonnées de toutes les données géométriques d’une source de données. Pour limiter la couche afin qu’elle n’affiche que les fonctionnalités de géométrie de point, définissez la propriété `filter` de la couche sur `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` si vous voulez également inclure les fonctionnalités MultiPoint.

Le gestionnaire de sprites d’images Maps, qui est utilisé pour charger les images personnalisées utilisées par le calque de symboles, prend en charge les formats d’image suivants :

- JPEG
- PNG
- SVG
- BMP
- GIF (sans animation)

## <a name="add-a-symbol-layer"></a>Ajouter un calque de symboles

Pour ajouter une couche de symboles à la carte et restituer des données, une source de données doit d’abord être créée et ajoutée à la carte. Une couche de symboles peut ensuite être créée et transmise dans la source de données à partir de laquelle récupérer les données. Enfin, les données doivent être ajoutées à la source de données pour qu’il y ait un rendu à afficher. Le code suivant affiche le code à ajouter au mappage après son chargement pour restituer un point unique sur la carte à l’aide d’une couche de symboles. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Il existe quatre types de données de point à qui peuvent être ajoutés à la carte :

- Géométrie des points GeoJSON - cet objet contient uniquement une coordonnée d’un point et rien d’autre. La classe d’assistance `atlas.data.Point` peut être utilisée pour créer facilement ces objets.
- Géométrie MultiPoint GeoJSON - cet objet contient les coordonnées de plusieurs points et rien d’autre. La classe d’assistance `atlas.data.MultiPoint` peut être utilisée pour créer facilement ces objets.
- Fonctionnalité GeoJSON - cet objet se compose d’une géométrie GeoJSON et d’un ensemble de propriétés qui contiennent des métadonnées associées à la géométrie. La classe d’assistance `atlas.data.Feature` peut être utilisée pour créer facilement ces objets.
- La classe `atlas.Shape` est similaire à la fonctionnalité GeoJSON, par le fait qu’elle se compose d’une géométrie GeoJSON et d’un ensemble de propriétés qui contiennent des métadonnées associées à la géométrie. Si un objet GeoJSON est ajouté à une source de données, il peut être facilement rendu dans une couche, toutefois, si la propriété des coordonnées de cet objet GeoJSON est mise à jour, la source de données et la carte ne changent pas, car il n’existe aucun mécanisme dans l’objet JSON pour déclencher une mise à jour. La classe Shape fournit des fonctions pour mettre à jour les données qu’elle contient, et lorsqu’une modification est apportée, la source de données et la carte sont automatiquement averties et mises à jour. 

L’exemple de code suivant crée une géométrie de points GeoJSON et la transmet dans la classe `atlas.Shape` pour faciliter sa mise à jour. Le centre de la carte est initialement utilisé pour restituer un symbole. Un événement de clic est ajouté à la carte, de sorte que lorsqu’il est déclenché, les coordonnées de l’endroit où l’utilisateur a cliqué sont utilisées avec la fonction `setCoordinates` Shapes qui met à jour l’emplacement du symbole sur la carte.

<br/>

<iframe height='500' scrolling='no' title='Switch pin location' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a> (Changer l’emplacement du repère) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Par défaut, pour améliorer les performances, les couches de symboles optimisent le rendu des symboles en masquant les symboles qui se chevauchent. Quand vous effectuez un zoom avant, les symboles masqués deviennent visibles. Pour désactiver cette fonctionnalité et afficher tous les symboles en permanence, définissez la propriété `allowOverlap` des options `iconOptions` sur `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Ajouter une icône personnalisée à une couche de symboles

Les couches de symboles sont affichées à l’aide de WebGL. Toutes les ressources, telles que les images d’icône, doivent donc être chargées dans le contexte de WebGL. Cet exemple montre comment ajouter une icône personnalisée dans les ressources de la carte, puis l’utiliser pour afficher les données de point avec un symbole personnalisé sur la carte. La propriété `textField` de la couche de symboles nécessite une expression. Dans le cas présent, nous voulons afficher la propriété de température, mais étant donné qu’il s’agit d’un nombre, elle doit être convertie en chaîne. De plus, nous voulons lui ajouter « °F ». Pour ce faire, une expression peut être utilisée : `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Icône de l’image de symbole personnalisée' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Le kit SDK web Azure Maps fournit plusieurs modèles d’image personnalisables que vous pouvez utiliser avec le calque de symboles. Pour plus d’informations, consultez le document [Guide pratique pour utiliser des modèles d’image](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-symbol-layer"></a>Personnaliser une couche de symboles 

Il y a de nombreuses options de style disponibles pour la couche de symboles. Voici un outil permettant de les tester.

<br/>

<iframe height='700' scrolling='no' title='Options de la couche de symboles' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consultez l’extrait de code <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Lorsque vous souhaitez afficher uniquement le texte avec une couche de symboles, vous pouvez masquer l’icône en réglant la propriété `image` des options d’icône sur `'none'`.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Pour obtenir plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Créer une source de données](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une fenêtre contextuelle](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utiliser des expressions de style basées sur les données](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Guide pratique pour utiliser des modèles d’image](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](map-add-shape.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Ajouter des marqueurs HTML](map-add-bubble-layer.md)
