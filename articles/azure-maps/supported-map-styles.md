---
title: Styles de cartes pris en charge dans Azure Maps | Microsoft Docs
description: Styles de cartes pris en charge par Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 923b9fef75fa610ee817a86ce0b4d6b322153f01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113429"
---
# <a name="azure-maps-supported-map-styles"></a>Styles de cartes pris en charge dans Azure Maps
Azure Maps prend en charge plusieurs styles de mappage intégrés, qui sont décrits ci-dessous.

## <a name="road"></a>Route
Une carte **routière** est une carte standard qui affiche les routes, les éléments cartographiques naturels et créés par l’homme, ainsi que le nom de ces éléments cartographiques.

![Route](./media/supported-map-styles/road.png)

**API applicables :**
* [Image de la carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Commandes de la carte JS

## <a name="satellite"></a>Satellite 
Le style **satellite** est une combinaison d’imagerie satellite et d’imagerie aérienne.

![Satellite](./media/supported-map-styles/satellite.png)

**API applicables :**
* [Vignette Satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Commandes de la carte JS

## <a name="satelliteroadlabels"></a>satellite_road_labels
Ce style de carte est un hybride qui montre des routes et des noms superposés à une imagerie satellite et aérienne.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**API applicables :**
* Commandes de la carte JS

## <a name="grayscaledark"></a>grayscale_dark
**Nuances de gris foncé** est une version plus sombre du style carte routière.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**API applicables :**
* Commandes de la carte JS 

## <a name="night"></a>nuit
**Nuit** est la version foncée du style de carte routière avec des routes et des symboles en couleur.

![nuit](./media/supported-map-styles/night.png)

**API applicables :**
* Commandes de la carte JS

## <a name="roadshadedrelief"></a>road_shaded_relief
Le **relief par ombres portées** est un style d’Azure Maps qui reprend le relief terrestre.

![relief par ombres portées](./media/supported-map-styles/shaded-relief.png)

**API applicables :**
* Commandes de la carte JS
* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
