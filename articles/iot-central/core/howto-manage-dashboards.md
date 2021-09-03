---
title: Créer et gérer des tableaux de bord Azure IoT Central | Microsoft Docs
description: Découvrez comment créer et gérer des tableaux de bord d’application et personnels.
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6c14cc00843a998990c89e7892a793d10cfdbbdf
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113650888"
---
# <a name="create-and-manage-dashboards"></a>Créer et gérer des tableaux de bord

Lorsque vous accédez à votre application pour la première fois, la page *Tableau de bord d’application* par défaut se charge. En tant qu’administrateur, vous pouvez créer jusqu’à 10 tableaux de bord d’application qui sont visibles par tous les utilisateurs de l’application. Seuls les administrateurs peuvent créer, modifier et supprimer des tableaux de bord de niveau application.

Tous les utilisateurs peuvent créer leurs propres *tableaux de bord personnels*. Les utilisateurs peuvent basculer entre les tableaux de bord d’application et les tableaux de bord personnels.

## <a name="create-dashboard"></a>Créer un tableau de bord

La capture d’écran suivante montre le tableau de bord dans une application créée à partir du modèle **Application personnalisée**. Vous pouvez remplacer le tableau de bord d’application par défaut par un tableau de bord personnel ou, si vous êtes administrateur, par un autre tableau de bord de niveau application. Pour ce faire, sélectionnez **+ Nouveau tableau de bord** en haut à gauche de la page :

:::image type="content" source="media/howto-manage-dashboards/dashboard-custom-app.png" alt-text="Capture d’écran d’un tableau de bord pour applications basé sur le modèle d’application personnalisée.":::

Sélectionnez **+ Nouveau tableau de bord** pour ouvrir l’éditeur du tableau de bord. Dans l’éditeur, nommez votre tableau de bord et choisissez des éléments dans la bibliothèque. La bibliothèque contient les vignettes et les primitives de tableau de bord que vous pouvez utiliser pour personnaliser le tableau de bord :

:::image type="content" source="media/howto-manage-dashboards/dashboard-library.png" alt-text="Capture d’écran qui montre la bibliothèque pour le tableau de bord.":::

Si vous êtes un administrateur, vous pouvez créer un tableau de bord personnel ou un tableau de bord d’application. Tous les utilisateurs de l’application peuvent voir les tableaux de bord d’application créés par l’administrateur. Tous les utilisateurs peuvent créer des tableaux de bord personnels qu’ils sont les seuls à pouvoir afficher.

Entrez un titre et sélectionnez le type de tableau de bord que vous souhaitez créer. [Ajoutez des vignettes](#add-tiles) pour personnaliser votre tableau de bord.

> [!TIP]
> Vous avez besoin d’au moins un modèle d’appareil dans votre application pour pouvoir ajouter des vignettes qui affichent les informations sur l’appareil.

## <a name="manage-dashboards"></a>Gérer les tableaux de bord

Vous pouvez disposer de plusieurs tableaux de bord personnels et basculer de l’un à l’autre, ou choisir l’un des tableaux de bord d’application :

:::image type="content" source="media/howto-manage-dashboards/switch-dashboards.png" alt-text="Capture d’écran qui montre comment basculer entre les tableaux de bord.":::

Vous pouvez modifier vos tableaux de bord personnels et supprimer ceux dont vous n’avez plus besoin. Si vous êtes **administrateur**, vous pouvez également modifier ou supprimer des tableaux de bord de niveau application.

:::image type="content" source="media/howto-manage-dashboards/delete-dashboards.png" alt-text="Capture d’écran qui montre comment supprimer des tableaux de bord.":::

## <a name="add-tiles"></a>Ajouter des vignettes

La capture d’écran suivante montre le tableau de bord dans une application créée à partir du modèle **Application personnalisée**. Pour personnaliser le tableau de bord actif, sélectionnez **Modifier**. Pour ajouter un tableau de bord personnel ou d’application, sélectionnez **Nouveau** :

:::image type="content" source="media/howto-manage-dashboards/dashboard-sample-contoso.png" alt-text="Tableau de bord des applications basées sur le modèle d’application personnalisée":::

Une fois que vous avez sélectionné **Modifier** ou **Nouveau**, le tableau de bord est en *mode d’édition*. Vous pouvez utiliser les outils du panneau **Modifier le tableau de bord** pour ajouter des vignettes au tableau de bord, personnaliser et supprimer des vignettes sur le tableau de bord lui-même. Par exemple, pour ajouter une vignette **Graphique en courbes** pour suivre les valeurs de télémétrie dans le temps signalées par un ou plusieurs appareils :

1. Sélectionnez **Démarrer avec un visuel**, puis choisissez **Graphique en courbes** et sélectionnez **Ajouter une vignette** ou glissez-déposez simplement la vignette dans le canevas.
 
1. Pour configurer la vignette, sélectionnez son icône d’engrenage. Entrez un **Titre** et sélectionnez un **Groupe d’appareils**, puis choisissez vos appareils dans la liste déroulante **Appareils** pour les afficher sur la vignette.

:::image type="content" source="media/howto-manage-dashboards/device-details.png" alt-text="Ajouter une vignette de télémétrie de température au tableau de bord":::

Une fois que vous avez sélectionné toutes les valeurs à afficher sur la vignette, cliquez sur **Mettre à jour**.

Une fois que vous avez fini d’ajouter et de personnaliser des vignettes sur le tableau de bord, sélectionnez **Enregistrer** pour enregistrer les modifications apportées au tableau de bord, ce qui vous permet de sortir du mode d’édition.

## <a name="customize-tiles"></a>Personnaliser des vignettes

Pour modifier une vignette, vous devez être en mode d’édition. Les options de personnalisation disponibles dépendent du [type de vignette](#tile-types) :

* L’icône de règle d’une vignette vous permet de modifier la visualisation. Les visualisations incluent les graphiques en courbes, les graphiques à barres, les graphiques à secteurs, la dernière valeur connue (LKV), l’indicateur de performance clé (KPI), la carte thermique et le mappage.

* L’icône carrée vous permet de redimensionner la vignette.

* L’icône d’engrenage vous permet de configurer la visualisation. Par exemple, pour un graphique en courbes, vous pouvez choisir d’afficher la légende et les axes, puis choisir l’intervalle de temps à tracer.

## <a name="tile-types"></a>Types de vignettes

Le tableau suivant décrit les différents types de vignettes que vous pouvez ajouter à un tableau de bord :

| Vignette             | Description |
| ---------------- | ----------- |
| Markdown         | Les vignettes Markdown sont des vignettes interactives qui affichent un en-tête et un texte de description formatés à l’aide de Markdown. L’URL peut être un lien relatif menant à une autre page de l’application, ou un lien absolu menant à un site externe.|
| Image            | Les vignettes d’image affichent une image personnalisée et peuvent être interactives. L’URL peut être un lien relatif menant à une autre page de l’application, ou un lien absolu menant à un site externe.|
| Étiquette            | Les vignettes d’étiquette affichent un texte personnalisé sur un tableau de bord. Vous pouvez choisir la taille du texte. Utilisez une vignette d’étiquette pour ajouter des informations pertinentes au tableau de bord, telles que des descriptions, des informations de contact ou une assistance.|
| Count            | Les vignettes Nombre affichent le nombre d’appareils qui se trouvent dans un groupe d’appareils.|
| Carte(télémétrie)              | Les vignettes Carte affichent l’emplacement d’un appareil sur une carte. Vous pouvez également afficher jusqu'à 100 points de l'historique des emplacements d'un appareil. Par exemple, vous pouvez afficher un itinéraire détaillant les emplacements d’un appareil au cours de la semaine dernière.|
| Carte(propriété)              | Les vignettes Carte affichent l’emplacement d’un appareil sur une carte.|
| KPI              |  Les vignettes KPI affichent des valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée. Par exemple, vous pouvez les utiliser pour afficher la température et la pression maximales atteintes par un ou plusieurs appareils durant la dernière heure.|
| Graphique en courbes       | Les vignettes Graphique en courbes tracent une ou plusieurs valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée. Par exemple, vous pouvez afficher un graphique en courbes pour tracer la température et la pression moyennes d’un ou plusieurs appareils au cours de la dernière heure.|
| Graphique à barres        | Les vignettes Graphique à barres tracent une ou plusieurs valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée. Par exemple, vous pouvez afficher un graphique à barres pour tracer la température et la pression moyennes d’un ou plusieurs appareils au cours de la dernière heure.|
| Graphique à secteurs        | Les vignettes Graphique à secteurs affichent une ou plusieurs valeurs de télémétrie agrégées pour un ou plusieurs appareils sur une période donnée.|
| Carte thermique         | Les vignettes Carte thermique présentent des informations sur un ou plusieurs appareils sous forme de couleurs.|
| Dernière valeur connue | Les vignettes Dernière valeur connue affichent les dernières valeurs de télémétrie pour un ou plusieurs appareils. Par exemple, vous pouvez utiliser cette vignette pour afficher les valeurs de température, de pression et d’humidité les plus récentes d’un ou plusieurs appareils. |
| Historique des événements    | Les vignettes d’historique des événements affichent les événements d’un appareil sur une période de temps. Par exemple, vous pouvez l’utiliser pour afficher tous les événements d’ouverture et de fermeture de vanne pour un ou plusieurs appareils au cours de la dernière heure.|
| Propriété         |  Les vignettes Propriété affichent la valeur actuelle des propriétés et propriétés cloud d’un ou plusieurs appareils. Par exemple, vous pouvez utiliser cette vignette pour afficher les propriétés d’un appareil, comme son fabricant ou la version de son microprogramme. |
| Graphique d’état         |  Le graphique d’état retrace les modifications apportées à un ou plusieurs appareils durant un intervalle de temps défini. Par exemple, vous pouvez utiliser cette vignette pour afficher les propriétés d’un appareil, comme les changements de température d’un appareil. |
| Graphique d’événement         |  Le graphique d’événement affiche les événements de télémétrie pour un ou plusieurs appareils durant un intervalle de temps défini. Par exemple, vous pouvez utiliser cette vignette pour afficher les propriétés, comme les changements de température d’un appareil. |
| Historique de l'état         |  L’historique de l’état répertorie et affiche les changements d’état pour la télémétrie État.|
| Contexte externe         |  La vignette Contenu externe vous permet de charger du contenu externe à partir d’une source externe. |

Actuellement, vous pouvez ajouter jusqu’à 10 appareils à des vignettes prenant en charge plusieurs appareils.

### <a name="customizing-visualizations"></a>Personnalisation des visualisations

Par défaut, les graphiques en courbes affichent les données sur un intervalle de temps. L’intervalle de temps sélectionné est fractionné en 50 compartiments de taille égale, et les données de l’appareil sont ensuite regroupées par compartiment pour fournir 50 points de données sur l’intervalle de temps sélectionné. Si vous souhaitez afficher les données brutes, vous pouvez modifier votre sélection pour afficher les 100 dernières valeurs. Pour modifier l’intervalle de temps ou pour sélectionner la visualisation des données brutes, utilisez la liste déroulante Intervalle d’affichage dans le panneau **Configurer le graphique**.

:::image type="content" source="media/howto-manage-dashboards/display-range.png" alt-text="Modifier l’intervalle d’affichage d’un graphique en courbes":::

Pour les vignettes affichant des valeurs agrégées, sélectionnez l’icône d’engrenage en regard du type de données de télémétrie dans le panneau **Configurer le graphique** afin de choisir l’agrégation. Vous pouvez choisir entre moyenne, somme, maximum, minimum et nombre.

Pour les graphiques en courbes, à barres et à secteurs, vous pouvez personnaliser la couleur des différentes valeurs de télémétrie. Sélectionnez l’icône de palette en regard des données de télémétrie que vous souhaitez personnaliser :

:::image type="content" source="media/howto-manage-dashboards/color-customization.png" alt-text="Modifier la couleur d’une valeur de télémétrie":::

Pour les vignettes affichant des propriétés de chaîne ou des valeurs de télémétrie, vous pouvez choisir comment afficher le texte. Par exemple, si l’appareil stocke une URL dans une propriété de chaîne, vous pouvez l’afficher sous forme de lien sur lequel vous pouvez cliquer. Si l’URL fait référence à une image, vous pouvez restituer celle-ci dans une vignette de dernière valeur connue ou de propriété. Pour modifier la manière dont une chaîne s’affiche, dans la configuration de la vignette, sélectionnez l’icône d’engrenage en regard du type de télémétrie ou de la propriété  :

:::image type="content" source="media/howto-manage-dashboards/string-customization.png" alt-text="Modifier la manière dont une chaîne s’affiche sur une vignette":::

Pour les vignettes numériques KPI, Dernière valeur connue et Propriété, vous pouvez utiliser la mise en forme conditionnelle pour personnaliser la couleur de la vignette en fonction de sa valeur actuelle. Pour ajouter une mise en forme conditionnelle, sélectionnez **Configurer** sur la vignette, puis sélectionnez l’icône **Mise en forme conditionnelle** en regard de la valeur à personnaliser :

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-1.png" alt-text="Capture d’écran montrant comment trouver l’option Configurer pour une vignette, puis l’icône de mise en forme conditionnelle":::

Ajoutez vos règles de mise en forme conditionnelle :

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-2.png" alt-text="Capture d’écran montrant les règles de mise en forme conditionnelle pour le flux moyen. Il existe trois règles : moins de 20 est vert, moins de 50 est jaune et tout ce qui se trouve au-dessus de 50 est rouge":::

La capture d’écran suivante montre l’effet de la règle de mise en forme conditionnelle :

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-3.png" alt-text="Capture d’écran montrant la couleur d’arrière-plan rouge sur la vignette Débit d’eau moyen. Le nombre sur la vignette est 50,54":::

### <a name="tile-formatting"></a>Mise en forme de la vignette

Cette fonctionnalité, disponible dans les vignettes KPI, LKV et Propriété, permet aux utilisateurs d’ajuster la taille de police, de choisir la précision décimale, d’abréger les valeurs numériques (par exemple, format 1 700 en 1,7K) ou de renvoyer à la ligne les valeurs de chaîne dans leurs vignettes.

:::image type="content" source="media/howto-manage-dashboards/tile-format.png" alt-text="Format de la vignette":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer et gérer des tableaux de bord personnels, vous pouvez [découvrir comment gérer vos préférences d’application](howto-manage-preferences.md).
