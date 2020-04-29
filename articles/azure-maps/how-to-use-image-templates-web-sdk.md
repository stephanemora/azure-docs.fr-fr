---
title: Modèles d’image dans le SDK web Azure Maps | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à utiliser des modèles d’image avec des marqueurs HTML et différentes couches dans le SDK web Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804807"
---
# <a name="how-to-use-image-templates"></a>Guide pratique pour utiliser des modèles d’image

Les images peuvent être utilisées avec des marqueurs HTML et différentes couches dans le SDK web Azure Maps :

 - Les couches de symboles peuvent afficher des points sur la carte avec une icône d’image. Les symboles peuvent également être rendus le long d’un tracé de lignes.
 - Les couches de polygones peuvent être rendues avec une image de motif de remplissage. 
 - Les marqueurs HTML peuvent afficher des points à l’aide d’images et d’autres éléments HTML.

Pour garantir de bonnes performances avec les couches, chargez les images dans la ressource de sprite d’image de carte avant le rendu. Par défaut, l’interface [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), de SymbolLayer, précharge dans le sprite d’image de carte quelques images de marqueur dans un certain nombre de couleurs. Ces images de marqueur, entre autres éléments, sont disponibles sous forme de modèles SVG. Elles peuvent être utilisées pour créer des images avec des échelles personnalisées ou être utilisées comme couleur principale et secondaire du client. Au total, 42 modèles d’images sont fournis : 27 icônes de symboles et 15 motifs de remplissage de polygones.

Il est possible d’ajouter des modèles d’image aux ressources de sprite d’image de carte à l’aide de la fonction `map.imageSprite.createFromTemplate`. Cette fonction permet de passer jusqu’à cinq paramètres :

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

L’`id` est un identificateur unique que vous créez. L’`id` est attribué à l’image quand elle est ajoutée au sprite d’image de cartes. Utilisez cet identificateur dans les couches pour spécifier la ressource d’image à restituer. `templateName` spécifie le modèle d’image à utiliser. L’option `color` définit la couleur principale de l’image et les options `secondaryColor` définissent la couleur secondaire de l’image. L’option `scale` met à l’échelle le modèle d’image avant de l’appliquer au sprite d’image. Quand l’image est appliquée au sprite d’image, elle est convertie en image PNG. Pour garantir un rendu clair, il est préférable d’appliquer un scale-up au modèle d’image avant de l’ajouter au sprite plutôt que dans une couche.

Cette fonction charge l’image de façon asynchrone dans le sprite d’image. Ainsi, elle retourne une promesse que vous pouvez attendre la fin de l’exécution de cette fonction.

Le code suivant montre comment créer une image à partir de l’un des modèles intégrés, et comment l’utiliser avec une couche de symboles.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Utiliser un modèle d’image avec une couche de symboles

Une fois qu’un modèle d’image a été chargé dans le sprite d’image de carte, il peut être restitué sous la forme d’un symbole dans une couche de symboles en référençant l’ID de ressource d’image dans l’option `image` de `iconOptions`.

L’exemple suivant restitue une couche de symboles à l’aide du modèle d’image `marker-flat` avec le bleu-vert comme couleur principale et le blanc comme couleur secondaire. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Couche de symboles avec un modèle d’icône intégré" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>Couche de symboles avec un modèle d’icône intégré</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Utiliser un modèle d’image le long d’un tracé de lignes

Une fois qu’un modèle d’image a été chargé dans le sprite d’image de carte, il peut être restitué le long du tracé d’une ligne en ajoutant un LineString à une source de données et en utilisant une couche de symboles avec une option `lineSpacing`, puis en référençant l’ID de la ressource d’image dans l’option `image` de `iconOptions`. 

L’exemple suivant restitue une ligne rose sur la carte et utilise une couche de symboles à l’aide du modèle d’image `car` avec le bleu toile comme couleur principale et le blanc comme couleur secondaire. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Couche de lignes avec un modèle d’icône intégré" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>Couche de lignes avec un modèle d’icône intégré</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Si le modèle d’image pointe vers le haut, définissez l’option icône `rotation` de la couche de symboles sur 90 si vous voulez qu’il pointe dans la même direction que la ligne.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Utiliser un modèle d’image avec une couche de polygones

Une fois qu’un modèle d’image a été chargé dans le sprite d’image de carte, il peut être restitué sous la forme d’un modèle de remplissage dans une couche de polygones en référençant l’ID de ressource d’image dans l’option `fillPattern` de la couche.

L’exemple suivant restitue une couche de polygones à l’aide du modèle d’image `dot` avec le rouge comme couleur principale et le transparent comme couleur secondaire.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Remplir un polygone avec un modèle d’icône intégré" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Remplir un polygone avec un modèle d’icône intégré</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> La définition de la couleur secondaire des motifs de remplissage permet de mieux voir la carte sous-jacente tout en fournissant néanmoins le modèle principal. 

## <a name="use-an-image-template-with-an-html-marker"></a>Utiliser un modèle d’image avec un marqueur HTML

Un modèle d’image peut être récupéré à l’aide de la fonction `altas.getImageTemplate` et utilisé comme contenu d’un marqueur HTML. Le modèle peut être passé à l’option `htmlContent` du marqueur, puis personnalisé à l’aide des options `color`, `secondaryColor` et `text`.

L’exemple suivant utilise le modèle `marker-arrow` avec le rouge comme couleur principale, le rose comme couleur secondaire et la valeur de texte «00».

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marqueur HTML avec un modèle d’icône intégré" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>Marqueur HTML avec un modèle d’icône intégré</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Créer des modèles réutilisables personnalisés

Si votre application utilise la même icône avec des icônes différentes ou si vous créez un module qui ajoute des modèles d’image supplémentaires, vous pouvez facilement ajouter et récupérer ces icônes à partir du SDK web Azure Maps. Utilisez les fonctions statiques suivantes sur l’espace de noms `atlas`.

| Nom | Type de retour | Description | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Ajoute un modèle d’image SVG personnalisé à l’espace de noms atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Récupère un modèle SVG par son nom. |
| `getAllImageTemplateNames()` | string[] |  Récupère un modèle SVG par son nom. |

Les modèles d’images SVG prennent en charge les valeurs d’espace réservé suivantes :

| Espace réservé | Description |
|-|-|
| `{color}` | La couleur principale. | 
| `{secondaryColor}` | La couleur secondaire. | 
| `{scale}` | L’image SVG est convertie en image PNG quand elle est ajoutée au sprite d’image de carte. Cet espace réservé peut être utilisé pour mettre à l’échelle un modèle avant qu’il ne soit converti afin de garantir qu’il s’affiche clairement. | 
| `{text}` | L’emplacement de rendu du texte quand il est utilisé avec un marqueur HTML. |

L’exemple suivant montre comment prendre un modèle SVG et l’ajouter au SDK web Azure Maps en tant que modèle d’icône réutilisable. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ajouter un modèle d’icône personnalisé à l’espace de noms atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Ajouter un modèle d’icône personnalisé à l’espace de noms atlas</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Liste des modèles d’image

Ce tableau liste tous les modèles d’image actuellement disponibles dans le SDK web Azure Maps. Le nom de modèle se trouve au-dessus de chaque image. Par défaut, la couleur principale est le bleu et la couleur secondaire est le blanc. Pour rendre la couleur secondaire plus visible sur un fond blanc, la couleur secondaire est définie sur noir pour les images suivantes.

**Modèles d’icône de symbole**

|||||
|:-:|:-:|:-:|:-:|
| marker | marker-thick | marker-circle | marker-flat |
|![icône marker](./media/image-templates/marker.png)|![icône marker-thick](./media/image-templates/marker-thick.png)|![icône marker-circle](./media/image-templates/marker-circle.png)|![icône marker-flat](./media/image-templates/marker-flat.png)|
||||
| marker-square | marker-square-cluster | marker-arrow | marker-ball-pin | 
|![icône marker-square](./media/image-templates/marker-square.png)|![icône marker-square-cluster](./media/image-templates/marker-square-cluster.png)|![icône marker-arrow](./media/image-templates/marker-arrow.png)|![icône marker-ball-pin](./media/image-templates/marker-ball-pin.png)|
||||
| marker-square-rounded | marker-square-rounded-cluster | indicateur | flag-triangle |
| ![icône marker-square-rounded](./media/image-templates/marker-square-rounded.png) | ![icône marker-square-rounded-cluster](./media/image-templates/marker-square-rounded-cluster.png) | ![icône flag](./media/image-templates/flag.png) | ![icône flag-triangle](./media/image-templates/flag-triangle.png) |
||||
| triangle | triangle-thick | triangle-arrow-up | triangle-arrow-left |
| ![icône triangle](./media/image-templates/triangle.png) | ![icône triangle-thick](./media/image-templates/triangle-thick.png) | ![icône triangle-arrow-up](./media/image-templates/triangle-arrow-up.png) | ![icône triangle-arrow-left](./media/image-templates/triangle-arrow-left.png) |
||||
| hexagon | hexagon-thick | hexagon-rounded | hexagon-rounded-thick |
| ![icône hexagon](./media/image-templates/hexagon.png) | ![icône hexagon-thick](./media/image-templates/hexagon-thick.png) | ![icône hexagon-rounded](./media/image-templates/hexagon-rounded.png) | ![icône hexagon-rounded-thick](./media/image-templates/hexagon-rounded-thick.png) |
||||
| pin | pin-round | rounded-square | rounded-square-thick |
| ![icône pin](./media/image-templates/pin.png) | ![icône pin-round](./media/image-templates/pin-round.png) | ![icône rounded-square](./media/image-templates/rounded-square.png) | ![icône rounded-square-thick](./media/image-templates/rounded-square-thick.png) |
||||
| arrow-up | arrow-up-thin | voiture ||
| ![icône arrow-up](./media/image-templates/arrow-up.png) | ![icône arrow-up-thin](./media/image-templates/arrow-up-thin.png) | ![icône car](./media/image-templates/car.png) | |

**Modèles de motif de remplissage de polygones**

|||||
|:-:|:-:|:-:|:-:|
| checker | checker-rotated | circles | circles-spaced |
| ![icône checker](./media/image-templates/checker.png) | ![icône checker-rotated](./media/image-templates/checker-rotated.png) | ![icône circles](./media/image-templates/circles.png) | ![icône circles-spaced](./media/image-templates/circles-spaced.png) |
|||||
| diagonal-lines-up | diagonal-lines-down | diagonal-stripes-up | diagonal-stripes-down |
| ![icône diagonal-lines-up](./media/image-templates/diagonal-lines-up.png) | ![icône diagonal-lines-down](./media/image-templates/diagonal-lines-down.png) | ![icône diagonal-stripes-up](./media/image-templates/diagonal-stripes-up.png) | ![icône diagonal-stripes-down](./media/image-templates/diagonal-stripes-down.png) |
|||||
| grid-lines | rotated-grid-lines | rotated-grid-stripes | x-fill |
| ![icône grid-lines](./media/image-templates/grid-lines.png) | ![icône rotated-grid-lines](./media/image-templates/rotated-grid-lines.png) | ![icône rotated-grid-stripes](./media/image-templates/rotated-grid-stripes.png) | ![icône x-fill](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-vertical | dots |  |
| ![icône zig-zag](./media/image-templates/zig-zag.png) | ![icône zig-zag-vertical](./media/image-templates/zig-zag-vertical.png) | ![icône dots](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Outil Essayer maintenant

Avec l’outil suivant, vous pouvez restituer les différents modèles d’image intégrés de différentes façons et personnaliser l’échelle et les couleurs principales et secondaires.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Options des modèles d’icône" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page Pen <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Options des modèles d’icône</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur le site <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Espace de noms atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Consultez les articles suivants pour obtenir d’autres exemples de code dans lesquels des modèles d’image peuvent être utilisés :

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](map-add-shape.md)

> [!div class="nextstepaction"]
> [Ajouter des marqueurs HTML](map-add-bubble-layer.md)
