---
title: Fonctionnalités de style de carte dans Azure Maps | Microsoft Docs
description: Apprenez-en davantage sur les fonctionnalités liées au style dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 78e3f6a5f4a55fd4bf1925672205eb490e7c6b24
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695691"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Choisir un style de carte dans Azure Maps

Azure Maps offre le choix entre quatre styles de carte. Pour plus d’informations sur les styles de carte, voir [Styles de carte pris en charge dans Azure Maps](./supported-map-styles.md). Cet article explique comment utiliser les fonctionnalités liées au style pour définir un style lors du chargement de carte, définir un nouveau style et utiliser le contrôle du sélecteur de styles.

## <a name="set-style-on-map-load"></a>Définir un style lors du chargement de carte

<iframe height='500' scrolling='no' title='Définition du style lors du chargement de carte' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Définition du style lors du chargement de carte</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le bloc de code ci-dessus définit la clé d’abonnement et crée un objet carte sans définir le style sur grayscale_dark. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

## <a name="update-the-style"></a>Mettre à jour le style

<iframe height='500' scrolling='no' title='Mise à jour du style' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Mise à jour du style</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le bloc de code ci-dessus définit la clé d’abonnement et crée un objet carte sans prédéfinir le style. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

La deuxième bloc de code utilise la méthode [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de la carte pour définir le style de carte sur satellite.

## <a name="add-the-style-picker"></a>Ajouter le sélecteur de styles

<iframe height='500' scrolling='no' title='Ajout du sélecteur de styles' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Voir la page <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Ajout du sélecteur de styles</a> d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

Le premier bloc de code dans les ensembles de code ci-dessus définit la clé d’abonnement et crée un objet carte avec le style de carte prédéfini sur grayscale_dark. Pour obtenir des instructions sur la création d’une carte, consultez [Créer une carte](./map-create.md).

Le deuxième bloc de code construit un sélecteur de styles à l’aide du constructeur [StyleControl](/javascript/api/azure-maps-control/atlas.controls.stylecontrol) Atlas.

Un sélecteur de styles permet de sélectionner un style pour la carte. Le troisième bloc de code ajoute le sélecteur de styles à la carte en utilisant la méthode [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de la carte. Le sélecteur de styles se situe dans le **détecteur d’événements** de la carte pour garantir le chargement une fois que la carte est entièrement chargée.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Ajouter un contrôle à carte :

> [!div class="nextstepaction"]
> [Ajouter des contrôles de carte](./map-add-controls.md)

Ajouter un repère de carte :

> [!div class="nextstepaction"]
> [Ajouter un repère](./map-add-pin.md)
