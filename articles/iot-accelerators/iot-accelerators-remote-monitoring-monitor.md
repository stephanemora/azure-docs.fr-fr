---
title: Superviser des appareils dans la solution de supervision à distance – Azure | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment surveiller vos appareils IoT avec l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 400a71b11fde210b889d938041e88c5ebe73c1dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890872"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Tutoriel : Surveiller vos appareils IoT

Dans ce tutoriel, vous utilisez l’accélérateur de solution de supervision à distance pour surveiller vos appareils IoT connectés. Vous utilisez le tableau de bord des solutions pour afficher des données de télémétrie, des informations sur les appareils, des alertes et des indicateurs de performance clés.

Le tutoriel utilise deux appareils simulés sur des camions, qui envoient l’emplacement, la vitesse et la télémétrie de température de la cargaison. Les camions sont gérés par une entreprise appelée Contoso et sont connectés à l’accélérateur de solution de supervision à distance. En tant qu’opérateur de Contoso, vous devez surveiller la position et le comportement d’un de vos camions (truck-02) sur la route.

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Filtrer les appareils dans le tableau de bord
> * Afficher les données de télémétrie en temps réel
> * Afficher les détails sur l’appareil
> * Afficher les alertes sur vos appareils
> * Afficher les indicateurs KPI système

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Choisir les appareils à afficher

Pour sélectionner les appareils connectés à afficher dans la page **Tableau de bord**, utilisez des filtres. Pour afficher uniquement les appareils de type **Truck** (camion), choisissez le filtre prédéfini **Trucks** (Camions) dans la liste déroulante :

[![Filtrer les camions sur le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Lorsque vous appliquez un filtre, seuls les appareils qui respectent les conditions de filtre s’affichent sur la carte et dans le panneau de télémétrie. Vous pouvez constater que deux camions sont connectés à l’accélérateur de solution, dont truck-02 :

[![Seuls les camions sont affichés sur la carte](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Pour créer, modifier et supprimer des filtres, cliquez sur **Gérer les groupes d’appareils**.

## <a name="view-real-time-telemetry"></a>Afficher les données de télémétrie en temps réel

L’accélérateur de solution trace les données de télémétrie en temps réel dans le graphique de la page **Tableau de bord**. Vous voyez en haut du graphique de télémétrie les types de télémétrie disponibles pour les appareils, dont truck-02, sélectionnés par le filtre actif. Par défaut, le graphique affiche la latitude des camions et truck-02 semble être à l’arrêt :

[![Types de télémétrie des camions](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Pour afficher les données de télémétrie de température des camions, cliquez sur **Température**. Vous pouvez consulter les variations de température de truck-02 au cours de la dernière heure :

[![Traçage des données de télémétrie de température des camions](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Afficher la carte

La carte affiche des informations sur les camions simulés sélectionnés par le filtre actif. Vous pouvez effectuer un zoom avant et un panoramique sur la carte pour afficher les positions avec plus ou moins de détails. La couleur d’une icône d’appareil sur la carte indique si des **Alertes** (bleu foncé) ou des **Avertissements** (rouge) sont actifs pour l’appareil. Un total du nombre **d’alertes** et **d’avertissements** est affiché à gauche de la carte.

Pour voir les détails sur truck-02, effectuez un panoramique et un zoom sur la carte pour le localiser, puis sélectionnez le camion sur la carte. Cliquez ensuite sur l’étiquette de l’appareil pour ouvrir le panneau **Détails de l’appareil**. Les détails de l’appareil incluent les éléments suivants :

* Valeurs de données de télémétrie récentes
* Méthodes prises en charge par l’appareil
* Propriétés de l’appareil

[![Afficher les détails des appareils dans le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Afficher les alertes

Le panneau **Alertes** affiche des informations détaillées sur les alertes les plus récentes de vos appareils. Les alertes provenant du camion truck-02 indiquent que la température de la cargaison est supérieure à la normale :

[![Afficher les alertes des appareils sur le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Vous pouvez utiliser un filtre pour délimiter l’intervalle de temps des alertes récentes. Par défaut, le panneau affiche les alertes de l’heure écoulée.

## <a name="view-the-system-kpis"></a>Afficher les indicateurs KPI système

La page **Tableau de bord** montre les indicateurs de performance clés du système calculés par l’accélérateur de solution dans le panneau **Analytique** :

[![Indicateurs de performance clés du tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Le tableau de bord montre trois indicateurs de performance clés pour les alertes sélectionnées par les filtres actuels pour l’appareil et l’intervalle de temps :

* Nombre d’alertes actives pour les règles qui ont déclenché le plus d’alertes.
* Proportion des alertes par type d’appareil.
* Pourcentage d’alertes qui sont des alertes critiques.

Pour truck-02, toutes les alertes sont des avertissements d’une température de la cargaison supérieure à la normale.

Les mêmes filtres que ceux qui définissent l’intervalle de temps pour les alertes et qui contrôlent quels les appareils sont affichés déterminent la façon dont les indicateurs de performance clés sont agrégés. Par défaut, le panneau affiche les KPI agrégés pendant la dernière heure écoulée.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment utiliser la page **Tableau de bord** de l’accélérateur de solution de supervision à distance pour filtrer et surveiller les camions simulés. Pour découvrir comment utiliser l’accélérateur de solution pour détecter des problèmes avec vos appareils connectés, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Détectez les problèmes liés aux appareils connectés à votre solution de supervision](iot-accelerators-remote-monitoring-automate.md)