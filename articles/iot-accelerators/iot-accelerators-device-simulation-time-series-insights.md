---
title: Visualiser la télémétrie de simulation d’appareil avec Azure Time Series Insights | Microsoft Docs
description: Découvrez comment configurer votre environnement Time Series Insights pour explorer et analyser la télémétrie générée par l’accélérateur de solution Simulation d’appareil.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 49cecca4e5ebef9f43fdda16cfa1fdc2ad7b6f94
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383033"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Time Series Insights permet de visualiser la télémétrie envoyée par l’accélérateur de solution Simulation d’appareil

L’accélérateur de solution Simulation d’appareil vous permet de générer une télémétrie à partir d’appareils simulés pour tester vos solutions IoT. Ce guide vous montre comment visualiser et analyser la télémétrie simulée à l’aide d’un environnement Time Series Insights.

## <a name="prerequisites"></a>Conditions préalables

Pour suivre les étapes décrites dans ce guide, vous avez besoin d’un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Les étapes décrites dans ce guide partent du principe que vous avez déployé l’accélérateur de solution Simulation d’appareil dans votre abonnement Azure. Si vous n’avez pas déployé l’accélérateur de solution, suivez les étapes décrites dans le guide de démarrage rapide [Déployer et exécuter une solution de simulation d’appareil basée sur le cloud](quickstart-device-simulation-deploy.md).

Cet article part du principe que le nom de votre accélérateur de solution est **contoso-simulation**. Dans le cadre des étapes suivantes, remplacez **contoso-simulation** par le nom de votre accélérateur de solution.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="start-a-simulation"></a>Démarrer une simulation

Avant d’utiliser l’Explorateur Time Series Insights, configurez l’accélérateur de solution Simulation d’appareil pour générer une télémétrie. La capture d’écran suivante présente une simulation en cours avec 10 appareils de type réfrigérateur :

![Exécution de simulation d’appareil](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Explorateur Time Series Insights

L’Explorateur Time Series Insights est une application web que vous pouvez utiliser pour visualiser votre télémétrie.

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de Time Series Insights.

1. Pour ouvrir l’application web Explorateur Time Series Insights, cliquez sur **Accéder à Environnement**:

    ![Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Dans le panneau de sélection des durées, dans le menu des durées courtes, sélectionnez **30 dernières minutes**, puis cliquez sur **Rechercher**:

    ![Recherche dans l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Dans le panneau des conditions à gauche, sélectionnez **Température** en tant que **Mesure**, et **iothub-connection-device-id** en tant que valeur pour **Divisé par** :

    ![Requête de l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Cliquez avec le bouton droit sur le graphique, puis sélectionnez **Explorer les événements**:

    ![Événements de l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Les données de l’événement s’affichent dans une grille :

    ![Tableau de l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Cliquez sur le bouton d’affichage en perspective :

    ![Perspective de l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Cliquez sur **+** pour ajouter une nouvelle requête à la perspective :

    ![Ajouter une requête dans l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Sélectionnez **30 dernières minutes** en tant qu’intervalle de temps, **Humidité** en tant que **Mesure**, et **iothub-connection-device-id** comme valeur pour **Divisé par**:

    ![Requête de l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Cliquez sur le bouton de perspective de vue pour afficher le tableau de bord de la télémétrie de votre appareil :

    ![Tableau de bord de l’Explorateur Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon d’explorer et d’interroger des données dans l’Explorateur Time Series Insights, voir [Explorateur Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
