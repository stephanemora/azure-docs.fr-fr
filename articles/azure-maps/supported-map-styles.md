---
title: Styles de cartes pris en charge dans Azure Maps | Microsoft Docs
description: Styles de cartes pris en charge par Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: concepts
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 33b0f5df57623f0b4433a4a09c7cd15688783485
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191597"
---
# <a name="azure-maps-supported-map-styles"></a>Styles de cartes pris en charge dans Azure Maps
Azure Maps prend en charge quatre styles de cartes intégrées. Ces styles sont listés ci-dessous, avec leur description.

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

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
Ce style de carte est un hybride qui montre des routes et des noms superposés à une imagerie satellite et aérienne.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**API applicables :**
* Commandes de la carte JS

## <a name="grayscaledark"></a>Grayscale_Dark
**Nuances de gris foncé** est une version plus sombre du style feuille de route.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**API applicables :**
* Commandes de la carte JS