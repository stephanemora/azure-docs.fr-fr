---
title: Styles de carte Azure Maps intégrés pris en charge
description: Dans cet article, vous allez découvrir les différents styles de carte intégrés pris en charge par Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b9641fe647eebae020ee67ee19a9cc131a487a7c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286887"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Styles de carte intégrés pris en charge par Azure Maps

Azure Maps prend en charge plusieurs styles de mappage intégrés, qui sont décrits ci-dessous.

## <a name="road"></a>Route

Une carte **routière** est une carte standard qui affiche les routes, les éléments cartographiques naturels et créés par l’homme ainsi que le nom de ces éléments cartographiques.

![style de carte routière](./media/supported-map-styles/road.png)

**API applicables :**

* [Image de la carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="blank-and-blank_accessible"></a>blank et blank_accessible

Les styles de carte **blank** et **blank_accessible** fournissent un canevas vide pour la visualisation des données. Le style **blank_accessible** continue de fournir des mises à jour de lecteur d’écran avec les détails de localisation de la carte, même si la carte de base n’est pas affichée.

> [!Note]
> Dans le SDK web, vous pouvez changer la couleur d’arrière-plan de la carte en définissant le style CSS `background-color` de l’élément DIV de la carte.

**API applicables :**

* Contrôle de carte du SDK web

## <a name="satellite"></a>Satellite

Le style **satellite** est une combinaison d’imagerie satellite et d’imagerie aérienne.

![style de carte vignette satellite](./media/supported-map-styles/satellite.png)

**API applicables :**

* [Vignette Satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels

Ce style de carte est un hybride qui montre des routes et des noms superposés à une imagerie satellite et aérienne.

![style de carte satellite_road_labels](./media/supported-map-styles/satellite-road-labels.png)

**API applicables :**

* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="grayscale_dark"></a>grayscale_dark

**Nuances de gris foncé** est une version plus sombre du style carte routière.

![style de carte gray_scale](./media/supported-map-styles/grayscale-dark.png)

**API applicables :**

* [Image de la carte](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="grayscale_light"></a>grayscale_light

**Nuances de gris clair** est une version plus claire du style carte routière.

![style de carte nuances de gris clair](./media/supported-map-styles/grayscale-light.png)

**API applicables :**
* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="night"></a>nuit

**Nuit** est la version foncée du style de carte routière avec des routes et des symboles en couleur.

![style de carte nuit](./media/supported-map-styles/night.png)

**API applicables :**

* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief

Le **relief par ombres portées** est un style d’Azure Maps qui reprend le relief terrestre.

![style de carte relief par ombres portées](./media/supported-map-styles/shaded-relief.png)

**API applicables :**

* [Vignette de la carte](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** est un style de carte sombre avec un contraste plus élevé que d’autres styles.

![style de carte sombre à contraste élevé](./media/supported-map-styles/high-contrast-dark.png)

**API applicables :**

* Contrôle de carte du SDK web
* Visuel Power BI

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment définir un style de carte dans Azure Maps :

> [!div class="nextstepaction"]
> [Choisir un style de carte](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
