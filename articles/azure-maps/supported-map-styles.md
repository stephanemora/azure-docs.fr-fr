---
title: Styles de carte Azure Maps intégrés pris en charge
description: Découvrez les styles de carte intégrés pris en charge par Azure Maps, comme route, blank_accessible, satellite, satellite_road_labels, road_shaded_relief et nuit.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 4032dec715fc12b4e8144e6d9eebd51faceaaaaf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562657"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Styles de carte intégrés pris en charge par Azure Maps

Azure Maps prend en charge plusieurs styles de mappage intégrés, qui sont décrits ci-dessous.

>[!important]
>Un compte Azure Maps du niveau tarifaire Gen1 ou Gen2 est nécessaire pour suivre la procédure de cette section. Pour plus d’informations sur les niveaux tarifaires, consultez [Choix du bon niveau tarifaire dans Azure Maps](choose-pricing-tier.md).

## <a name="road"></a>Route

Une carte **routière** est une carte standard qui affiche les routes, les éléments cartographiques naturels et créés par l’homme ainsi que le nom de ces éléments cartographiques.

![style de carte routière](./media/supported-map-styles/road.png)

**API applicables :**

* [Image de la carte](/rest/api/maps/render/getmapimage)
* [Vignette de la carte](/rest/api/maps/render/getmaptile)
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

* [Vignette Satellite](/rest/api/maps/render/getmapimagerytilepreview)
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

* [Image de la carte](/rest/api/maps/render/getmapimage)
* [Vignette de la carte](/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="grayscale_light"></a>grayscale_light

**Nuances de gris clair** est une version plus claire du style carte routière.

![style de carte nuances de gris clair](./media/supported-map-styles/grayscale-light.jpg)

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

* [Vignette de la carte](/rest/api/maps/render/getmaptile)
* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** est un style de carte sombre avec un contraste plus élevé que d’autres styles.

![style de carte sombre à contraste élevé](./media/supported-map-styles/high-contrast-dark.png)

**API applicables :**

* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="high_contrast_light"></a>high_contrast_light

**high_contrast_light** est un style de carte clair avec un contraste plus élevé que d’autres styles.

![style de carte clair à contraste élevé](./media/supported-map-styles/high-contrast-light.jpg)

**API applicables :**

* Contrôle de carte du SDK web
* Commandes de carte Android
* Visuel Power BI

## <a name="map-style-accessibility"></a>Accessibilité du style de carte

Les contrôles de carte Azure Maps interactifs utilisent des tuiles vectorielles dans les styles de carte pour alimenter le lecteur d’écran et décrire la zone affichée par la carte. Plusieurs styles de carte sont également conçus pour être entièrement accessibles en ce qui concerne le contraste des couleurs. Le tableau suivant fournit des détails sur les fonctionnalités d’accessibilité prises en charge par chaque style de carte.

| Style de carte  | Contraste des couleurs | Lecteur d’écran | Notes |
|------------|----------------|---------------|-------|
| `blank` | N/A | Non | Canevas vide utile pour les développeurs qui souhaitent utiliser leurs propres tuiles comme carte de base ou souhaitent afficher leurs données sans aucun arrière-plan. Le lecteur d’écran ne s’appuie pas sur les tuiles vectorielles pour les descriptions.  |
| `blank_accessible` | N/A  | Oui | En arrière-plan, ce style de carte continue à charger les tuiles vectorielles utilisées pour le rendu de la carte, mais rend ces données transparentes. De cette façon, les données sont toujours chargées et peuvent être utilisées pour alimenter le lecteur d’écran. |
| `grayscale_dark` | Partiel | Oui | Ce style de carte est principalement conçu pour les scénarios décisionnels, mais reste utile pour superposer des couches de couleur telles que des images radar météo. |
| `grayscale_light` | Partiel | Oui | Ce style de carte est principalement conçu pour les scénarios décisionnels. |
| `high_contrast_dark` | Oui | Oui | Style de carte entièrement accessible pour les utilisateurs en mode de contraste élevé avec un réglage sombre. Quand la carte se charge, les réglages à contraste élevé sont détectés automatiquement. |
| `high_contrast_light` | Oui | Oui | Style de carte entièrement accessible pour les utilisateurs en mode de contraste élevé avec un réglage clair. Quand la carte se charge, les réglages à contraste élevé sont détectés automatiquement. |
| `night` | Partiel | Oui | Ce style est conçu pour les situations où l’utilisateur est dans des conditions de faible éclairage et que vous ne souhaitez pas l’éblouir avec une carte trop claire. |
| `road` | Partiel | Oui | Il s’agit du style principal de carte routière en couleur dans Azure Maps. En raison du nombre de couleurs et de combinaisons de couleurs pouvant se chevaucher, il est presque impossible de le rendre accessible à 100 %. Cela dit, ce style de carte passe par un test d’accessibilité normal et est amélioré si nécessaire pour augmenter la lisibilité des légendes. |
| `road_shaded_relief` | Partiel | Oui | Il s’agit presque du même style que le style principal de carte routière, mais il a une couche de tuiles supplémentaire en arrière-plan qui ajoute un relief par ombres portées des montagnes et une coloration de la couverture terrestre lors d’un zoom arrière à des niveaux supérieurs. |
| `satellite` | N/A | Oui | Imagerie satellite et aérienne pure, sans légendes ni lignes de route. Les tuiles vectorielles sont chargées en arrière-plan pour alimenter le lecteur d’écran et assurer une transition plus fluide lors du basculement vers `satellite_with_roads`. |
| `satellite_with_roads` | Non | Oui | Imagerie satellite et aérienne, avec légendes et lignes de route superposées. À l’échelle mondiale, il existe un nombre illimité de combinaisons de couleurs susceptibles d’apparaître entre les données superposées et les images. L’accent est mis sur la lisibilité des légendes dans les scénarios les plus courants. Toutefois, à certains endroits, le contraste des couleurs avec l’image en arrière-plan peut rendre certaines légendes difficiles à lire. |

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment définir un style de carte dans Azure Maps :

> [!div class="nextstepaction"]
> [Choisir un style de carte](./choose-map-style.md)
