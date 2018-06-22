---
title: Surveillance avancée dans la solution de surveillance à distance - Azure | Microsoft Docs
description: Ce didacticiel vous montre comment surveiller les appareils à l’aide du tableau de bord de la solution de surveillance à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 02/22/2018
ms.topic: conceptual
ms.openlocfilehash: 4d2dabd348d7fda4fa7ca3aac9975fd4179400c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627397"
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Effectuer une surveillance avancée à l’aide de la solution de surveillance à distance

Ce tutoriel montre les fonctionnalités du tableau de bord de la solution de surveillance à distance. Pour présenter ces fonctionnalités, le tutoriel utilise un scénario dans l’application Contoso IoT.

Dans ce tutoriel, vous utilisez deux appareils Contoso simulés pour apprendre à surveiller vos appareils à partir du tableau de bord de l’accélérateur de solution. En tant qu’opérateur de Contoso, vous devez surveiller la position et le comportement de vos camions sur la route.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Filtrage des appareils dans le tableau de bord
> * Affichage des données de télémétrie en temps réel
> * Affichage des détails sur l’appareil
> * Affichage des alertes à partir de vos appareils
> * Affichage des KPI du système

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez suivre le tutoriel [Déployer l’accélérateur de solution de surveillance à distance](iot-accelerators-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Choisir les appareils à afficher

Pour sélectionner les appareils à afficher sur la page **Tableau de bord**, utilisez des filtres. Pour afficher uniquement les appareils de type **Truck** (camion), choisissez le filtre prédéfini **Trucks** (Camions) dans la liste déroulante :

![Filtrer les camions sur le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter.png)

Lorsque vous appliquez un filtre, seuls les appareils qui respectent les conditions de filtre s’affichent sur la carte de la page **Tableau de bord** :

![Affichage de camions sur la carte](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap.png)

Le filtre détermine également les appareils que vous voyez dans le graphique de **télémétrie** :

![Affichage des données de télémétrie des camions sur le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry.png)

Pour créer, modifier et supprimer des filtres, choisissez **Gérer les filtres**.

## <a name="view-real-time-telemetry"></a>Affichage des données de télémétrie en temps réel

L’accélérateur de solution trace les données détaillées de télémétrie en temps réel dans le graphique de la page **Tableau de bord**. Le graphique de télémétrie affiche des informations de télémétrie pour les appareils sélectionnés par le filtre actif :

![Tracé de télémétrie des camions](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview.png)

Pour sélectionner les valeurs de télémétrie à afficher, choisissez le type de données de télémétrie en haut du graphique :

![Tracé de télémétrie des camions](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>Utiliser la carte

La carte affiche des informations sur les camions simulés sélectionnés par le filtre actif. Vous pouvez effectuer un zoom avant et un panoramique sur la carte pour afficher les positions avec plus ou moins de détails. Les icônes d’appareil sur la carte indiquent les **alertes** et les **avertissements** qui sont activés pour l’appareil. Un récapitulatif du nombre **d’alertes** et **d’avertissements** est affiché à gauche de la carte.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Affichage des alertes à partir de vos appareils

La carte met en évidence les appareils du filtre actif qui sont associés à des **alertes** et à des **avertissements**. Le panneau **Alertes** affiche des informations détaillées sur les alertes les plus récentes de vos appareils :

![Afficher des alertes système dans le tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms.png)

Vous pouvez utiliser le filtre **Tableau de bord** pour n’afficher que les alertes récentes. Par défaut, le panneau affiche les alertes de l’heure écoulée :

![Filtrer les alertes par période](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Affichage des KPI du système

La page **Tableau de bord** affiche les KPI du système :

![KPI du tableau de bord](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis.png)

Vous pouvez utiliser le filtre **Tableau de bord** pour ajuster la période d’agrégation des KPI. Par défaut, le panneau affiche les KPI agrégés pendant la dernière heure écoulée.

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment utiliser la page **Tableau de bord** pour filtrer et surveiller les camions simulés provisionnés dans votre solution de surveillance à distance :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrage des appareils dans le tableau de bord
> * Affichage des données de télémétrie en temps réel
> * Affichage des détails sur l’appareil
> * Affichage des alertes à partir de vos appareils
> * Affichage des KPI du système

La surveillance de vos appareils n’ayant plus de secrets pour vous, nous vous suggérons de découvrir les opérations suivantes :

* [Détecter les problèmes à l’aide de règles de seuil](iot-accelerators-remote-monitoring-automate.md)
* [Gérer et configurer vos appareils](iot-accelerators-remote-monitoring-manage.md)
* [Dépanner et corriger les problèmes d’un appareil](iot-accelerators-remote-monitoring-maintain.md)
* [Tester votre solution avec des appareils simulés](iot-accelerators-remote-monitoring-test.md)

<!-- Next tutorials in the sequence -->