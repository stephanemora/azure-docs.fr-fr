---
title: Styles de cartes pris en charge dans Azure Maps | Microsoft Docs
description: Styles de cartes pris en charge par Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c8edaba8de597e3e76e760e1f5109006338a663c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238818"
---
# <a name="azure-maps-supported-map-styles"></a>Styles de cartes pris en charge dans Azure Maps
Azure Maps prend en charge quatre styles de cartes intégrées. Les styles sont répertoriés ci-dessous, avec leur description.

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