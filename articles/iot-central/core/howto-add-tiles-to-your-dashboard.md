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
ms.openlocfilehash: 49b41715d95a5f210e6e70faf09aa016d1478728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158741"
---
# <a name="configure-the-application-dashboard"></a>Configurer le tableau de bord de l’application

Le **tableau de bord** est la page qui se charge quand les utilisateurs autorisés accèdent à l’URL de l’application. Si vous avez créé votre application à partir de l’un des **modèles d’application**, votre application dispose d’un tableau de bord prédéfini pour commencer. Si vous avez créé votre application à partir du modèle d’application **Application héritée**, votre tableau de bord est vide pour commencer.

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
1. Sélectionnez un **Modèle d’appareil**.
1. Sélectionnez une **Instance d’appareil** pour l’appareil que vous souhaitez afficher sur une vignette du tableau de bord. La liste des propriétés de l’appareil utilisables sur la vignette s’affiche alors.
1. Pour créer la vignette sur le tableau de bord, cliquez sur **Température** et faites-la glisser dans la zone du tableau de bord. Vous pouvez également cocher la case à côté de **Température** et cliquer sur **Combiner**. La capture d’écran suivante montre comment sélectionner un modèle d’appareil et une instance d’appareil, puis créer une vignette Télémétrie de température sur le tableau de bord.
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
| Mappage | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes de carte affichent l’emplacement et l’état d’un appareil sur une carte. Par exemple, vous pouvez voir l’endroit où se trouve un appareil et si son ventilateur est allumé.|
| Graphique en courbes | Tableaux de bord des appareils et applications |Les vignettes de graphique en courbes affichent un graphique des mesures agrégées d’un appareil pour une période de temps. Par exemple, vous pouvez afficher un graphique en courbes montrant la température et la pression moyennes d’un appareil au cours de la dernière heure.|
| Graphique à barres | Tableaux de bord des appareils et applications |Les vignettes de graphique à barres affichent un graphique des mesures agrégées d’un appareil pour une période de temps. Par exemple, vous pouvez afficher un graphique à barres montrant la température et la pression moyennes d’un appareil au cours de la dernière heure.|
| Graphique à secteurs | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes de graphique à secteur affichent un graphique des mesures agrégées d’un appareil pour une période de temps.|
| Carte thermique | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes de carte thermique affichent des informations sur un ensemble d’appareils, représentées sous forme de couleurs.|
| Historique des événements | Tableaux de bord des appareils et applications |Les vignettes d’historique des événements affichent les événements d’un appareil sur une période de temps. Par exemple, vous pouvez en utiliser pour afficher tous les changements de température qui se sont produits pour un appareil au cours de la dernière heure.|
| Historique de l'état | Tableaux de bord des appareils et applications |Les vignettes d’historique d’état affichent les valeurs mesurées pour une période de temps. Par exemple, vous pouvez l’utiliser pour afficher que les valeurs de température d’un appareil durant la dernière heure.|
| KPI | Tableaux de bord des appareils et applications | Les vignettes d’indicateur de performance clé affichent une mesure de télémétrie ou d’événement agrégée pour une période de temps. Par exemple, vous pouvez l’utiliser pour afficher la température maximale atteinte par un appareil durant la dernière heure.|
| Dernière valeur connue | Tableaux de bord des appareils et applications |Les vignettes de dernière valeur connue affichent la dernière valeur d’une mesure de télémétrie ou d’état. Par exemple, vous pouvez utiliser cette vignette pour afficher les mesures de température, de pression et d’humidité les plus récentes d’un appareil.|

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment configurer votre tableau de bord d’application par défaut Azure IoT Central, découvrez comment [Créer un tableau de bord personnel](howto-create-personal-dashboards.md).
