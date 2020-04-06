---
title: Changer le style de la carte dans Azure Maps | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir les fonctionnalités de style disponibles dans le kit de développement logiciel (SDK) web Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335683"
---
# <a name="change-the-style-of-the-map"></a>Changer le style de la carte

La carte prend en charge différentes [options de style](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) qui peuvent être définies une fois la carte initialisée ou ultérieurement, à l’aide de la fonction `setStyle`. Cet article explique comment utiliser ces options de style pour personnaliser l’apparence des cartes. Apprenez à définir un style lors du chargement d’une carte et à paramétrer un nouveau style de carte à l’aide du contrôle de sélecteur de styles.

## <a name="set-the-style-options"></a>Définir les options de style 

Les options de style peuvent être transmises à la carte lors de son initialisation ou d'une mise à jour ultérieure, à l'aide de la fonction `setStyle`.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

L’outil suivant explique la manière dont les différentes options de style modifient l'affichage de la carte. Pour afficher les bâtiments 3D, effectuez un zoom avant à proximité d'une agglomération. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Options de style de carte" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez l’extrait <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Options de style de carte</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Sélectionner un style de carte de base

L’une des options de style de carte les plus courantes permet de changer le style de la carte de base. Un grand nombre des [styles de carte pris en charge dans Azure Maps](supported-map-styles.md) sont disponibles dans le SDK web. 

### <a name="set-base-map-style-on-map-load"></a>Définir le style de la carte de base lors du chargement de la carte


Le style de la carte peut être spécifié lors de l’initialisation de la carte en définissant l’option `style`. Dans le code suivant, l’option `style` de la carte est définie sur `grayscale_dark` au moment de l’initialisation.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Définition du style lors du chargement de carte' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Définition du style lors du chargement de carte</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Mettre à jour le style de carte de base

 Le style de carte peut être mis à jour en utilisant la fonction `setStyle` et en définissant l’option `style` sur le style souhaité.

```javascript
map.setStyle({ style: 'satellite' });
```

Dans le code suivant, après le chargement d’une instance de carte, le style de carte est mis à jour de `road` en `satellite` à l’aide de la fonction [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-).

<br/>

<iframe height='500' scrolling='no' title='Mise à jour du style' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Mise à jour du style</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Ajouter le sélecteur de styles

Le contrôle de sélecteur de styles fournit un bouton facile à utiliser doté d'un panneau volant dont l'utilisateur final peut se servir pour modifier le style de carte. Le sélecteur de styles propose deux options de disposition distinctes. Par défaut, le sélecteur de styles utilise la disposition `icons` et affiche le style de carte sous forme de ligne horizontale d’icônes. 

<center>

![Disposition des icônes du sélecteur de styles](media/choose-map-style/style-picker-icon-layout.png)</center>

La seconde option de disposition est appelée `list` et affiche une liste déroulante de styles de carte.  

<center>

![Disposition de la liste du sélecteur de styles](media/choose-map-style/style-picker-list-layout.png)</center>


Le code suivant montre comment créer une instance du contrôle de sélecteur de styles et l’ajouter dans le coin supérieur droit de la carte. Le sélecteur de styles est défini pour présenter un style sombre et afficher quelques styles de carte sélectionnés à l’aide de la couche de liste.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Le code suivant ajoute un contrôle de sélecteur de styles avec paramètres par défaut à la carte pour permettre à l’utilisateur de facilement basculer entre les différents styles de carte. Basculez le style de carte à l’aide du contrôle de style de carte dans le coin supérieur droit.

<br/>

<iframe height='500' scrolling='no' title='Ajout du sélecteur de styles' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Ajout du sélecteur de styles</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Par défaut, le contrôle de sélecteur de styles répertorie tous les styles disponibles quand vous utilisez le niveau tarifaire S0 d’Azure Maps. Si vous voulez réduire le nombre de styles figurant dans cette liste, passez dans l’option `mapStyle` du sélecteur de styles un tableau des styles que vous voulez voir apparaître dans la liste. Si vous utilisez S1 et que vous voulez afficher tous les styles disponibles, affectez à l’option `mapStyles` du sélecteur de styles la valeur `"all"`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Ajouter des contrôle à vos cartes :

> [!div class="nextstepaction"]
> [Ajouter des contrôles de carte](map-add-controls.md)

> [!div class="nextstepaction"]
> [Ajouter un repère](map-add-pin.md)
