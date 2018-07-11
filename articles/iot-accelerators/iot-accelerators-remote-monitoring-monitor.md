---
title: Surveiller vos appareils IoT à partir d’une solution Azure | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment surveiller vos appareils IoT avec l’accélérateur de solution Monitoring à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097459"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Tutoriel : Surveiller vos appareils IoT

Dans ce tutoriel, vous utilisez l’accélérateur de solution Monitoring à distance pour surveiller vos appareils IoT connectés. Vous utilisez le tableau de bord des solutions pour afficher des données de télémétrie, des informations sur les appareils, des alertes et des indicateurs de performance clés.

Pour présenter ces fonctionnalités de monitoring, le tutoriel utilise deux appareils simulés sur des camions. Les camions sont gérés par une entreprise appelée Contoso et sont connectés à l’accélérateur de solution Monitoring à distance. En tant qu’opérateur de Contoso, vous devez surveiller la position et le comportement de vos camions sur la route.

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Filtrer les appareils dans le tableau de bord
> * Afficher les données de télémétrie en temps réel
> * Afficher les détails sur l’appareil
> * Afficher les alertes sur vos appareils
> * Afficher les indicateurs KPI système

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’une instance déployée de l’accélérateur de solution Monitoring à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé l’accélérateur de solution Monitoring à distance, vous devez suivre le guide de démarrage rapide [Déployer une solution de monitoring à distance cloud](quickstart-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Choisir les appareils à afficher

Pour sélectionner les appareils connectés à afficher dans la page **Tableau de bord**, utilisez des filtres. Pour afficher uniquement les appareils de type **Truck** (camion), choisissez le filtre prédéfini **Trucks** (Camions) dans la liste déroulante :

[![Filtrer les camions sur le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Quand vous appliquez un filtre, seuls les appareils qui correspondent aux conditions du filtre sont affichés sur la carte de la page **Tableau de bord** :

[![Seuls les camions sont affichés sur la carte](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Le filtre détermine également les appareils que vous voyez dans le graphique de **télémétrie** :

[![Les données de télémétrie des camions sont affichées sur le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

Pour créer, modifier et supprimer des filtres, choisissez **Gérer les groupes d’appareils**.

## <a name="view-real-time-telemetry"></a>Afficher les données de télémétrie en temps réel

L’accélérateur de solution trace les données de télémétrie en temps réel dans le graphique de la page **Tableau de bord**. Vous voyez en haut du graphique de télémétrie les types de télémétrie disponibles pour les appareils sélectionnés par le filtre actif :

[![Types de télémétrie des camions](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Pour afficher les données de télémétrie de température, cliquez sur **Temperature**:

[![Traçage des données de télémétrie de température des camions](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>Utiliser la carte

La carte affiche des informations sur les camions simulés sélectionnés par le filtre actif. Vous pouvez effectuer un zoom avant et un panoramique sur la carte pour afficher les positions avec plus ou moins de détails. La couleur d’une icône d’appareil sur la carte indique si des **Alertes** ou des **Avertissements** sont actifs pour l’appareil. Un total du nombre **d’alertes** et **d’avertissements** est affiché à gauche de la carte.

Pour voir les détails des appareils, effectuez un panoramique et un zoom sur la carte pour localiser les appareils, puis sélectionnez l’appareil sur la carte. Cliquez ensuite sur l’étiquette de l’appareil pour ouvrir le panneau **Détails de l’appareil**. Les détails de l’appareil incluent les éléments suivants :

* Valeurs de données de télémétrie récentes
* Méthodes prises en charge par l’appareil
* Propriétés de l’appareil

[![Afficher les détails des appareils dans le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Afficher les alertes

Le panneau **Alertes** affiche des informations détaillées sur les alertes les plus récentes de vos appareils :

[![Afficher les alertes des appareils sur le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Vous pouvez utiliser un filtre pour délimiter l’intervalle de temps des alertes récentes. Par défaut, le panneau affiche les alertes de l’heure écoulée :

[![Filtrer les alertes intervalle de temps](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>Afficher les indicateurs KPI système

La page **Tableau de bord** montre les indicateurs de performance clés du système calculés par l’accélérateur de solution dans le panneau **Analytique** :

[![Indicateurs de performance clés du tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Le tableau de bord montre trois indicateurs de performance clés pour les alertes sélectionnées par les filtres actuels pour l’appareil et l’intervalle de temps :

* Nombre d’alertes actives pour les règles qui ont déclenché le plus d’alertes.
* Proportion des alertes par type d’appareil.
* Pourcentage d’alertes qui sont des alertes critiques.

Les mêmes filtres que ceux qui définissent l’intervalle de temps pour les alertes et qui contrôlent quels les appareils sont affichés déterminent la façon dont les indicateurs de performance clés sont agrégés. Par défaut, le panneau affiche les KPI agrégés pendant la dernière heure écoulée.

## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous envisagez de passer au tutoriel suivant, laissez l’accélérateur de solution Monitoring à distance déployé. Pour réduire les coûts d’exécution de l’accélérateur de solution pendant que vous ne l’utilisez pas, vous pouvez arrêter les appareils simulés dans le panneau des paramètres :

[![Mettre en pause la télémétrie](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

Vous pouvez redémarrer les appareils simulés quand vous êtes prêt à commencer le tutoriel suivant.

Si vous n’avez plus besoin de l’accélérateur de solution, supprimez-le dans la page [Solutions approvisionnées](https://www.azureiotsolutions.com/Accelerators#dashboard) :

![Supprimer la solution](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment utiliser la page **Tableau de bord** de l’accélérateur de solution Monitoring à distance pour filtrer et surveiller les camions simulés. Pour découvrir comment utiliser l’accélérateur de solution pour détecter des problèmes avec vos appareils connectés, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Détectez les problèmes liés aux appareils connectés à votre solution de monitoring](iot-accelerators-remote-monitoring-automate.md)