---
title: Ajouter un calque de carte thermique au visuel Azure Maps pour Power BI | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à utiliser le calque de carte thermique dans le visuel Microsoft Azure Maps pour Power BI.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.custom: ''
ms.openlocfilehash: 37b03d96e67016bb9a250cd889da93fb30578d54
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240183"
---
# <a name="add-a-heat-map-layer-to-the-azure-maps-visual-for-power-bi"></a>Ajouter un calque de carte thermique au visuel Azure Maps pour Power BI

Dans cet article, vous allez apprendre à ajouter un calque de carte thermique à un visuel Azure Maps dans Power BI.

:::image type="content" source="media/power-bi-visual/heat-map.png" alt-text="Calque de carte thermique dans un visuel Azure Maps pour Power BI.":::

Les cartes thermiques, également appelées cartes de densité, sont un type de superposition sur une carte utilisé pour représenter la densité de données à l’aide de différentes couleurs. Les cartes thermiques sont souvent utilisées pour afficher les « zones réactives » des données sur une carte. Les cartes thermiques sont un excellent moyen de restituer des jeux de données dotés d’un grand nombre de points. L’affichage d’un grand nombre de points de données sur une carte entraîne une dégradation des performances et peut recouvrir la carte de symboles qui se chevauchent, ce qui la rend inutilisable. Le rendu des données sous la forme d’une carte thermique entraîne non seulement de meilleures performances, mais vous aide à mieux comprendre les données en vous permettant de voir plus facilement la densité relative de chaque point de données.

Une carte thermique est utile lorsque les utilisateurs souhaitent visualiser de grandes quantités de données comparatives :

- Comparer les taux de satisfaction des clients et les performances des boutiques entre des régions et des pays.
- Mesurer la fréquence à laquelle les clients visitent des centres commerciaux à des endroits différents.
- Visualiser de vastes jeux de données statistiques et géographiques.

## <a name="prerequisites"></a>Prérequis

- [Prise en main du visuel Azure Maps pour Power BI](/azure/azure-maps/power-bi-visual-getting-started).
- [Présentation des calques dans le visuel Azure Maps pour Power BI](/azure/azure-maps/power-bi-visual-understanding-layers).

## <a name="add-the-heat-map-layer"></a>Ajouter le calque de carte thermique

1. Dans Power BI Desktop, sélectionnez la carte Azure que vous avez créée.
1. Dans le volet **Format**, placez le commutateur **Carte thermique** sur **Activé**.

Vous pouvez maintenant ajuster tous les paramètres du calque de carte thermique pour les adapter à votre rapport.

## <a name="heat-map-layer-settings"></a>Paramètres du calque de carte thermique

La section **Carte thermique** du volet **Format** offre une certaine souplesse pour personnaliser et concevoir les visualisations de la carte thermique afin de répondre à vos besoins spécifiques. La section **Carte thermique** vous permet de :

- Configurer le rayon de chaque point de données en utilisant des pixels ou des mètres comme unité de mesure.
- Personnaliser l’opacité et l’intensité du calque de carte thermique.  
- Spécifier si la valeur du champ de taille doit être utilisée comme pondération de chaque point de données.
- Choisir différentes couleurs dans les sélecteurs de couleurs.
- Définir le niveau de zoom minimal et maximal pour afficher le calque de carte thermique.
- Déterminer la position du calque de carte thermique parmi les différents calques, par exemple, calque de graphique à barres 3D et calque de bulles.

Le tableau suivant présente les principaux paramètres de la section **Carte thermique** du volet **Format** :

| Paramètre              | Description      |
|----------------------|------------------|
| Radius | Le rayon de chaque point de données dans la carte thermique.<br /><br />Valeurs valides quand Unité = pixels : 1-200. Valeur par défaut : **20**<br />Valeurs valides quand Unité = mètres : 1 - 4 000 000|
| Unités  | Unités de distance du rayon. Les valeurs possibles sont les suivantes :<br /><br />**pixels**. Quand la valeur est définie sur pixels, la taille de chaque point de données est toujours la même, quel que soit le niveau de zoom.<br />**mètres**. Quand la valeur est définie sur mètres, la taille des points de données est mise à l’échelle en fonction du niveau de zoom, garantissant ainsi que le rayon est spatialement précis.<br /><br /> Valeur par défaut : **pixels**  |
| Opacity | Définit l’opacité du calque de carte thermique. Valeur par défaut : **1**<br/>La valeur doit être un nombre décimal compris entre 0 et 1. |
| Intensité | Intensité de chaque point de température. L’intensité est une valeur décimale comprise entre 0 et 1, utilisée pour spécifier la « chaleur » d’un point de données unique. Valeur par défaut : **0,5** |
| Utiliser la taille comme pondération | Valeur booléenne qui détermine si la valeur du champ de taille doit être utilisée comme pondération de chaque point de données. Si cette option est activée, le calque est rendu sous la forme d’une carte thermique pondérée. Valeur par défaut : **Désactivé** |
| Dégradé |Sélecteur de couleurs permettant aux utilisateurs de choisir 3 couleurs pour les couleurs de dégradé faible (0 %), central (50 %) et élevé (100 %). |
| Zoom minimal |Niveau de zoom minimal auquel le calque est visible. Les valeurs valides sont comprises entre 1 et 22. Valeur par défaut : **0** |
|Zoom maximal |Niveau de zoom maximal auquel le calque est visible.  Les valeurs valides sont comprises entre 1 et 22. Valeur par défaut : **22**|
|Position du calque |Indiquez la position du calque par rapport aux autres calques de la carte. Les valeurs valides incluent **Sur les étiquettes**, **Sous les étiquettes** et **Sous les routes** |

## <a name="next-steps"></a>Étapes suivantes

Modifier la façon dont vos données sont affichées sur la carte :

> [!div class="nextstepaction"]
> [Ajouter un calque de graphique à barres](power-bi-visual-add-bar-chart-layer.md)

Ajouter davantage de contexte à la carte :

> [!div class="nextstepaction"]
> [Ajouter une couche de référence](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de mosaïques](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Afficher le trafic en temps réel](power-bi-visual-show-real-time-traffic.md)

Personnaliser le visuel :

> [!div class="nextstepaction"]
> [Astuces et conseils pour la mise en forme des couleurs dans Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personnaliser les titres, les légendes et les arrière-plans des visualisations](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
