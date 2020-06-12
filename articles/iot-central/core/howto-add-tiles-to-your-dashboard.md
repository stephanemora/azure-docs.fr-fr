---
title: Ajouter des vignettes à votre tableau de bord Azure IoT Central | Microsoft Docs
description: En tant que concepteur, découvrez comment configurer le tableau de bord de l’application d’Azure IoT Central par défaut avec des vignettes.
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 892bdcc08bd19b92c8b3d32d2954583f80005e87
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022866"
---
# <a name="configure-the-application-dashboard"></a>Configurer le tableau de bord de l’application

Le **tableau de bord** est la première page que vous voyez lorsque vous vous connectez à une application Azure IoT Central. Si vous avez créé votre application à partir de l’un des [modèles d’application](./concepts-app-templates.md) axés sur le secteur, votre application dispose d’un tableau de bord prédéfini pour commencer. Si vous créez votre application à partir d’un [modèle d’application](./concepts-app-templates.md) personnalisé, votre tableau de bord affiche quelques conseils pour commencer.

> [!TIP]
> Les utilisateurs peuvent [créer plusieurs tableaux de bord](howto-create-personal-dashboards.md) en plus du tableau de bord par défaut de l’application. Ces tableaux de bord peuvent être personnels pour l’utilisateur ou partagés par tous les utilisateurs de l’application.  

## <a name="add-tiles"></a>Ajouter des vignettes

La capture d’écran suivante montre le tableau de bord dans une application créée à partir du modèle **Application personnalisée**. Pour personnaliser le tableau de bord actuel, sélectionnez **Modifier**, pour ajouter un tableau de bord personnalisé ou partagé, sélectionnez **Nouveau**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Tableau de bord des applications basées sur le modèle d’application personnalisée":::

Une fois que vous avez sélectionné **Modifier** ou **Nouveau**, le tableau de bord est en *mode d’édition*. Vous pouvez utiliser les outils du panneau **Modifier le tableau de bord** pour ajouter des vignettes au tableau de bord, personnaliser et supprimer des vignettes sur le tableau de bord lui-même. Par exemple, pour ajouter une vignette **Télémétrie** afin d’afficher la température actuelle signalée par un ou plusieurs appareils :

1. Dans le panneau **Modifier le tableau de bord**, sélectionnez un **groupe d’appareils**.
1. Dans la liste déroulante **Appareils**, sélectionnez un ou plusieurs appareils à afficher sur la vignette. Vous voyez maintenant les données de télémétrie, propriétés et commandes disponibles à partir des appareils.
1. Sélectionnez **Température** dans la section des données de télémétrie, puis **Ajouter une vignette**. La vignette s’affiche maintenant dans le tableau de bord où vous pouvez modifier la visualisation, redimensionner la vignette et la configurer :

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Ajouter une vignette de télémétrie de température au tableau de bord":::

Lorsque vous avez terminé d’ajouter des vignettes au tableau de bord ou de les personnaliser, sélectionnez **Enregistrer**.

## <a name="customize-tiles"></a>Personnaliser des vignettes

Pour personnaliser une vignette sur le tableau de bord, celui-ci doit être en mode édition. Les options de personnalisation disponibles dépendent du [type de vignette](#tile-types) :

* L’icône de règle d’une vignette vous permet de modifier la visualisation. Parmi les visualisations figurent les graphiques en courbes, les dernières valeurs connues et les cartes thermiques.

* L’icône carrée vous permet de redimensionner la vignette.

* L’icône d’engrenage vous permet de configurer la visualisation. Par exemple, pour une visualisation de graphique en courbes, vous pouvez choisir d’afficher la légende et les axes, puis choisir l’intervalle de temps à tracer.

## <a name="tile-types"></a>Types de vignettes

Le tableau suivant décrit les différents types de vignettes que vous pouvez ajouter à un tableau de bord :

| Vignette             | Description |
| ---------------- | ----------- |
| Markdown         | Les vignettes Markdown sont des vignettes interactives qui affichent un en-tête et un texte de description formatés à l’aide de Markdown. L’URL peut être un lien relatif menant à une autre page de l’application, ou un lien absolu menant à un site externe.|
| Image            | Les vignettes d’image affichent une image personnalisée et peuvent être interactives. L’URL peut être un lien relatif menant à une autre page de l’application, ou un lien absolu menant à un site externe.|
| Étiquette            | Les vignettes d’étiquette affichent un texte personnalisé sur un tableau de bord. Vous pouvez choisir la taille du texte. Utilisez une vignette d’étiquette pour ajouter des informations pertinentes au tableau de bord, telles que des descriptions, des informations de contact ou une assistance.|
| Count            | Les vignettes Nombre affichent le nombre d’appareils qui se trouvent dans un groupe d’appareils.|
| Mappage              | Les vignettes Carte affichent l’emplacement d’un appareil sur une carte. Vous pouvez également afficher jusqu'à 100 points de l'historique des emplacements d'un appareil. Par exemple, vous pouvez afficher un itinéraire détaillant les emplacements d’un appareil au cours de la semaine dernière.|
| KPI              |  Les vignettes KPI affichent des valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée. Par exemple, vous pouvez les utiliser pour afficher la température et la pression maximales atteintes par un ou plusieurs appareils durant la dernière heure.|
| Graphique en courbes       | Les vignettes Graphique en courbes tracent une ou plusieurs valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée. Par exemple, vous pouvez afficher un graphique en courbes pour tracer la température et la pression moyennes d’un ou plusieurs appareils au cours de la dernière heure.|
| Graphique à barres        | Les vignettes Graphique à barres tracent une ou plusieurs valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée. Par exemple, vous pouvez afficher un graphique à barres pour tracer la température et la pression moyennes d’un ou plusieurs appareils au cours de la dernière heure.|
| Graphique à secteurs        | Les vignettes Graphique à secteurs affichent une ou plusieurs valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée.|
| Carte thermique         | Les vignettes Carte thermique présentent des informations sur un ou plusieurs appareils sous forme de couleurs.|
| Dernière valeur connue | Les vignettes Dernière valeur connue affichent les dernières valeurs de télémétrie pour un ou plusieurs appareils. Par exemple, vous pouvez utiliser cette vignette pour afficher les valeurs de température, de pression et d’humidité les plus récentes d’un ou plusieurs appareils. |
| Historique des événements    | Les vignettes d’historique des événements affichent les événements d’un appareil sur une période de temps. Par exemple, vous pouvez l’utiliser pour afficher tous les événements d’ouverture et de fermeture de vanne pour un ou plusieurs appareils au cours de la dernière heure.|
| Propriété         |  Les vignettes Propriété affichent la valeur actuelle des propriétés et propriétés cloud d’un ou plusieurs appareils. Par exemple, vous pouvez utiliser cette vignette pour afficher les propriétés d’un appareil, comme son fabricant ou la version de son microprogramme. |

Actuellement, vous pouvez ajouter jusqu’à 10 appareils à des vignettes prenant en charge plusieurs appareils.

### <a name="customizing-visualizations"></a>Personnalisation des visualisations

Pour les vignettes affichant des valeurs agrégées, sélectionnez l’icône d’engrenage en regard du type de données de télémétrie dans le panneau **Configurer le graphique** afin de choisir l’agrégation. Vous pouvez choisir entre moyenne, somme, maximum, minimum et nombre.

Pour les graphiques en courbes, à barres et à secteurs, vous pouvez personnaliser la couleur des différentes valeurs de télémétrie. Sélectionnez l’icône de palette en regard des données de télémétrie que vous souhaitez personnaliser :

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Modifier la couleur d’une valeur de télémétrie":::

Pour les vignettes affichant des propriétés de chaîne ou des valeurs de télémétrie, vous pouvez choisir comment afficher le texte. Par exemple, si l’appareil stocke une URL dans une propriété de chaîne, vous pouvez l’afficher sous forme de lien sur lequel vous pouvez cliquer. Si l’URL fait référence à une image, vous pouvez restituer celle-ci dans une vignette de dernière valeur connue ou de propriété. Pour modifier la manière dont une chaîne s’affiche, dans la configuration de la vignette, sélectionnez l’icône d’engrenage en regard du type de télémétrie ou de la propriété  :

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Modifier la manière dont une chaîne s’affiche sur une vignette":::

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment configurer votre tableau de bord d’application par défaut Azure IoT Central, découvrez comment [Créer un tableau de bord personnel](howto-create-personal-dashboards.md).
