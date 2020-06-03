---
title: Ajouter des vignettes à votre tableau de bord | Microsoft Docs
description: En tant que concepteur, découvrez comment configurer le tableau de bord de l’application d’Azure IoT Central par défaut.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659101"
---
# <a name="configure-the-application-dashboard"></a>Configurer le tableau de bord de l’application

Le **tableau de bord** est la page qui se charge quand les utilisateurs autorisés accèdent à l’URL de l’application. Si vous avez créé votre application à partir de l’un des **modèles d’application**, votre application dispose d’un tableau de bord prédéfini pour commencer. Si vous avez créé votre application à partir du modèle d’**application personnalisée**, votre tableau de bord affiche quelques conseils pour commencer.

> [!NOTE]
> Les utilisateurs peuvent [créer plusieurs tableaux de bord](howto-create-personal-dashboards.md) en plus du tableau de bord par défaut de l’application. Ces tableaux de bord peuvent être personnels pour l’utilisateur ou partagés par tous les utilisateurs de l’application.  

## <a name="add-tiles"></a>Ajouter des vignettes

La capture d’écran suivante montre le tableau de bord dans une application créée à partir du modèle **Application personnalisée**. Pour personnaliser le tableau de bord par défaut pour votre application, sélectionnez **Modifier** en haut à gauche de la page.

> [!div class="mx-imgBorder"]
> ![Tableau de bord pour les applications basées sur le modèle « Exemple Contoso »](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

La sélection de **Modifier** ouvre le panneau de la bibliothèque de tableaux de bord. La bibliothèque contient les vignettes et les primitives de tableau de bord que vous pouvez utiliser pour personnaliser le tableau de bord.

> [!div class="mx-imgBorder"]
> ![Bibliothèque de tableaux de bord](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Par exemple, vous pouvez ajouter une vignette **Télémétrie** pour la température actuelle de l’appareil. Pour ce faire :

1. Sélectionnez un **modèle d’appareil**.
1. Sélectionnez dans **Appareils** l’appareil que vous souhaitez afficher sur une vignette de tableau de bord. La liste des propriétés de l’appareil utilisables sur la vignette s’affiche alors.
1. Pour créer la vignette sur le tableau de bord, cliquez sur **Température** et faites-la glisser dans la zone du tableau de bord. Vous pouvez également activer la case à cocher en regard de **Température** et cliquer sur **Ajouter une vignette**. La capture d’écran suivante montre comment sélectionner un modèle d’appareil et une instance d’appareil, puis créer une vignette Télémétrie de température sur le tableau de bord.
1. Sélectionnez **Enregistrer** en haut à gauche pour enregistrer la vignette dans le tableau de bord.

> [!div class="mx-imgBorder"]
> ![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](media/howto-add-tiles-to-your-dashboard/device-details.png)

Désormais, quand un opérateur affiche le tableau de bord de l’application par défaut, il voit la nouvelle vignette **Température** pour l’appareil. Chaque vignette a un graphique, un diagramme et d’autres paramètres présélectionnés qui apparaissent lors de sa création. Toutefois, les utilisateurs peuvent choisir de modifier et cette visualisation.  

> [!div class="mx-imgBorder"]
> ![Onglet « Tableau de bord » affichant les paramètres et les propriétés de la vignette](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Modifier des vignettes

Pour modifier une vignette sur le tableau de bord, cliquez d’abord sur **Modifier** en haut à gauche de la page. Cela a pour effet d’ouvrir le mode édition pour le tableau de bord et toutes ses vignettes.  

> [!div class="mx-imgBorder"]
> ![Écran du tableau de bord avec le mode édition activé pour une vignette sélectionnée](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Cliquez ensuite sur l’icône d’**engrenage** dans l’angle supérieur droit de la vignette à modifier. Ici, vous pouvez modifier divers aspects de la vignette, dont son titre, sa visualisation, son agrégation, etc.

> [!div class="mx-imgBorder"]
> ![Liste déroulante des paramètres d’agrégation d’une vignette](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Vous pouvez également modifier la visualisation de graphique en cliquant sur l’icône **Règle** sur la vignette.

> [!div class="mx-imgBorder"]
> ![Liste déroulante des paramètres de visualisation d’une vignette](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Types de vignettes

Le tableau suivant résume l’utilisation des vignettes dans Azure IoT Central :

| Vignette | tableau de bord | Description
| ----------- | ------- | ------- |
| Contenu | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes Markdown prises en charge sont des vignettes interactives qui affichent un en-tête et un texte de description. Vous pouvez utiliser cette vignette en tant que lien pour permettre aux utilisateurs d’accéder à une URL associée à votre application.|
| Image | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes d’image affichent une image personnalisée et peuvent être interactives. Utilisez une vignette d’image pour ajouter des graphiques à un tableau de bord et éventuellement permettre aux utilisateurs d’accéder à une URL pertinente pour votre application.|
| Étiquette | Tableaux de bord d’applications |Les vignettes d’étiquette affichent un texte personnalisé sur un tableau de bord. Vous pouvez choisir la taille du texte. Utilisez une vignette d’étiquette pour ajouter des informations pertinentes au tableau de bord, telles que des descriptions, des informations de contact ou une assistance.|
| Mappage | Tableaux de bord des appareils et applications |Les vignettes de carte affichent l’emplacement d’un appareil sur une carte. Vous pouvez également afficher jusqu'à 100 points de l'historique des emplacements d'un appareil. Par exemple, vous pouvez afficher un itinéraire détaillant les emplacements d’un appareil au cours de la semaine dernière.|
| Graphique en courbes | Tableaux de bord des appareils et applications |Les vignettes de graphique en courbes affichent un graphique des mesures agrégées d’un appareil pour une période de temps. Par exemple, vous pouvez afficher un graphique en courbes montrant la température et la pression moyennes d’un appareil au cours de la dernière heure.|
| Graphique à barres | Tableaux de bord des appareils et applications |Les vignettes de graphique à barres affichent un graphique des mesures agrégées d’un appareil pour une période de temps. Par exemple, vous pouvez afficher un graphique à barres montrant la température et la pression moyennes d’un appareil au cours de la dernière heure.|
| Graphique à secteurs | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes de graphique à secteur affichent un graphique des mesures agrégées d’un appareil pour une période de temps.|
| Carte thermique | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes de la carte thermique présentent des informations sur l’appareil sous la forme de couleurs.|
| Historique des événements | Tableaux de bord des appareils et applications |Les vignettes d’historique des événements affichent les événements d’un appareil sur une période de temps. Par exemple, vous pouvez en utiliser pour afficher tous les changements de température qui se sont produits pour un appareil au cours de la dernière heure.|
| Historique de l'état | Tableaux de bord des appareils et applications |Les vignettes d’historique d’état affichent les valeurs mesurées pour une période de temps. Par exemple, vous pouvez l’utiliser pour afficher que les valeurs de température d’un appareil durant la dernière heure.|
| KPI | Tableaux de bord des appareils et applications | Les vignettes d’indicateur de performance clé affichent une mesure de télémétrie ou d’événement agrégée pour une période de temps. Par exemple, vous pouvez l’utiliser pour afficher la température maximale atteinte par un appareil durant la dernière heure.|
| Dernière valeur connue | Tableaux de bord des appareils et applications |Les vignettes de dernière valeur connue affichent la dernière valeur d’une mesure de télémétrie ou d’état. Par exemple, vous pouvez utiliser cette vignette pour afficher les mesures de température, de pression et d’humidité les plus récentes d’un appareil. |
| Propriété | Tableaux de bord des appareils et applications | Les vignettes de propriété affichent la valeur actuelle des propriétés et propriétés cloud d’un appareil. Par exemple, vous pouvez utiliser cette vignette pour afficher les propriétés d’un appareil, comme son fabricant ou la version de son microprogramme. |

### <a name="customizing-visualizations"></a>Personnalisation des visualisations

Pour les graphiques en courbes, à barres et à secteurs, vous pouvez personnaliser les couleurs des différentes télémétries. Pour ce faire, sélectionnez l’icône de palette en regard de la télémétrie à personnaliser, puis choisissez une couleur.

> [!div class="mx-imgBorder"]
> ![Liste déroulante des paramètres d’affichage des couleurs de télémétrie](media/howto-add-tiles-to-your-dashboard/color-customization.png)

Pour les télémétries ou propriétés de type chaîne, vous pouvez choisir le mode de visualisation du texte. Par exemple, si votre appareil envoie une URL en guise de télémétrie de type chaîne, vous pouvez afficher cette URL sous la forme d’un lien actif. Si l’URL fait référence à une image, vous pouvez restituer celle-ci dans une vignette de dernière valeur connue ou de propriété. Vous pouvez modifier la façon dont la télémétrie de type chaîne s’affiche en sélectionnant l’engrenage en regard du nom de la télémétrie. De cette façon, vous pouvez afficher le texte sous forme de texte, de lien ou d’image.

> [!div class="mx-imgBorder"]
> ![Liste déroulante des paramètres de visualisation d’une chaîne](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment configurer votre tableau de bord d’application par défaut Azure IoT Central, découvrez comment [Créer un tableau de bord personnel](howto-create-personal-dashboards.md).
