---
title: Fonctionnalités de style de carte dans Azure Maps | Microsoft Docs
description: Apprenez-en davantage sur les fonctionnalités liées au style dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b95e3c325160d1c0b775320e25fdd40514b02b9f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666869"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Choisir un style de carte dans Azure Maps
Azure Maps offre le choix entre quatre styles de carte. Pour plus d’informations sur les styles de carte, voir [Styles de carte pris en charge dans Azure Maps](./supported-map-styles.md). Cet article explique comment utiliser les fonctionnalités liées au style pour définir un style lors du chargement de carte, définir un nouveau style et utiliser le contrôle du sélecteur de styles.

## <a name="setting-style-on-map-load"></a>Définition d’un style lors du chargement de carte

<iframe height='500' scrolling='no' title='Définition du style lors du chargement de carte' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Définition du style lors du chargement de carte</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le code ci-dessus crée un objet carte avec le style défini sur Nuances de gris. Pour obtenir des instructions sur la façon de créer une carte, voir [Créer une carte](./map-create.md).

## <a name="updating-the-style"></a>Mise à jour du style

<iframe height='500' scrolling='no' title='Mise à jour du style' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Mise à jour du style</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code dans le code ci-dessus crée un objet carte sans prédéfinition du style. Pour obtenir des instructions sur la façon de créer une carte, voir [Créer une carte](./map-create.md).

La deuxième horloge de code utilise la méthode [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) de la carte pour définir le style de carte sur satellite.

## <a name="adding-the-style-picker"></a>Ajout du sélecteur de styles

<iframe height='500' scrolling='no' title='Ajout du sélecteur de styles' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Ajout du sélecteur de styles</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code dans le code ci-dessus crée un objet carte sans prédéfinir le style. Pour obtenir des instructions sur la façon de créer une carte, voir [Créer une carte](./map-create.md).

Le deuxième bloc de code construit un sélecteur de styles à l’aide du constructeur [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) Atlas.

Un sélecteur de styles permet de sélectionner un style pour la carte. Le troisième bloc de code ajoute le sélecteur de styles à la carte en utilisant la méthode [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) de la carte.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article : 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
Pour consulter plus d’exemples de code à ajouter à vos cartes, consultez les articles suivants :
* [Ajouter des contrôles de carte](./map-add-controls.md)
* [Ajouter un repère](./map-add-pin.md)
