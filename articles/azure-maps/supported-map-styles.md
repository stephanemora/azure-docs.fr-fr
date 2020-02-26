---
title: Styles de cartes pris en charge | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir les différents styles de rendu de carte pris en charge par Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9cdfd0d029057e36e010203b7c35a5aafee4b574
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208282"
---
# <a name="azure-maps-supported-map-styles"></a>Styles de cartes pris en charge dans Azure Maps
Azure Maps prend en charge plusieurs styles de mappage intégrés, qui sont décrits ci-dessous.

## <a name="road"></a>Route
Une carte **routière** est une carte standard qui affiche les routes, les éléments cartographiques naturels et créés par l’homme, ainsi que le nom de ces éléments cartographiques.

![style de carte routière](./media/supported-map-styles/road.png)

**API applicables :**
* [Image de la carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web
* Commandes de carte Android

## <a name="blank-and-blank_accessible"></a>blank et blank_accessible

Les styles de carte **blank** et **blank_accessible** fournissent un canevas vide sur lequel visualiser les données. Le style **blank_accessible** continue de fournir des mises à jour de lecteur d’écran avec les détails d’emplacement de la carte, même si la carte de base n’est pas affichée.

> [!Note]
> Dans le SDK web, vous pouvez modifier la couleur d’arrière-plan de la carte en définissant le style `background-color` CSS de l’élément div de la carte.

**API applicables :**
* Contrôle de carte du SDK web

## <a name="satellite"></a>Satellite 
Le style **satellite** est une combinaison d’imagerie satellite et d’imagerie aérienne.

![style de carte vignette satellite](./media/supported-map-styles/satellite.png)

**API applicables :**
* [Vignette Satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Contrôle de carte du SDK web
* Commandes de carte Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Ce style de carte est un hybride qui montre des routes et des noms superposés à une imagerie satellite et aérienne.

![style de carte satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**API applicables :**
* Contrôle de carte du SDK web
* Commandes de carte Android

## <a name="grayscale_dark"></a>grayscale_dark
**Nuances de gris foncé** est une version plus sombre du style carte routière.

![style de carte gray_scale](./media/supported-map-styles/grayscale_dark.png)

**API applicables :**
* [Image de la carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web 
* Commandes de carte Android


## <a name="grayscale_light"></a>grayscale_light
**Nuances de gris clair** est une version plus claire du style carte routière.

![style de carte nuances de gris clair](./media/supported-map-styles/grayscale_light.png)

**API applicables :**
* Contrôle de carte du SDK web
* Commandes de carte Android


## <a name="night"></a>nuit
**Nuit** est la version foncée du style de carte routière avec des routes et des symboles en couleur.

![style de carte nuit](./media/supported-map-styles/night.png)

**API applicables :**
* Contrôle de carte du SDK web
* Commandes de carte Android

## <a name="road_shaded_relief"></a>road_shaded_relief
Le **relief par ombres portées** est un style d’Azure Maps qui reprend le relief terrestre.

![style de carte relief par ombres portées](./media/supported-map-styles/shaded-relief.png)

**API applicables :**
* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web
* Commandes de carte Android


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment définir un style de carte dans Azure Maps :

> [!div class="nextstepaction"]
> [Choisir un style de carte](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
