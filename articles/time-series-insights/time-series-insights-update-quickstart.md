---
title: 'Démarrage rapide : Explorer l’environnement de démonstration Azure Time Series Insights (préversion) | Microsoft Docs'
description: Découvrez comment fonctionne l’environnement de démonstration Azure Time Series Insights (préversion).
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276824"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Démarrage rapide : Explorer l’environnement de démonstration Azure Time Series Insights (préversion)

Ce guide de démarrage rapide vous montre comment utiliser l’Explorateur Azure Time Series Insights (préversion) dans un environnement de démonstration gratuit. Vous allez découvrir comment utiliser votre navigateur web pour visualiser de gros volumes de données IoT sectorielles historiques avant de faire le tour des fonctionnalités clés de l’explorateur Time Series Insights (préversion).

Time Series Insights constitue une offre PaaS (Platform as a Service) de bout en bout. Elle est utilisée pour ingérer, traiter, stocker et interroger des données à l’échelle IoT optimisées pour une série chronologique et hautement contextualisées dans le cadre d’une exploration improvisée des données. Elle fournit également des fonctions d’analyse opérationnelle. Time Series Insights est une offre différenciée adaptée aux besoins uniques des déploiements IoT sectoriels.

L’environnement de démonstration met en lumière une société de production d’électricité, Contoso. Au sein de cet environnement, Time Series Insights est utilisé pour rechercher dans les données de Contoso des insights exploitables et effectuer une brève analyse des causes premières des problèmes. Contoso exploite deux parcs éoliens qui se composent chacun de 10 éoliennes, chacune dotée de 20 capteurs qui transmettent des données toutes les minutes à Azure IoT Hub. Les capteurs recueillent des informations sur les conditions météorologiques, l’angle de calage et de lacet des pales, les performances de la génératrice, le comportement du multiplicateur et les moniteurs de sécurité.

Time Series Insights (préversion) permet à Contoso d’analyser les jeux de données toujours plus volumineux des deux dernières années – actuellement d’une taille de 40 Go, afin de mieux comprendre et prédire les défaillances critiques et les problèmes de maintenance lents.

Si vous n’avez pas d’abonnement Azure, créez un  [compte Azure gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  avant de commencer.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Découvrir l’Explorateur Time Series Insights dans un environnement de démonstration

1. Dans votre navigateur, accédez à  [l’environnement Contoso Wind Farm (Parc éolien Contoso)](https://insights.timeseries.azure.com/preview/samples).  

1. Si vous y êtes invité, connectez-vous à l’Explorateur Time Series Insights à l’aide de vos informations d’identification de compte Azure.

### <a name="demo-step-1"></a>Étape 1 de la démonstration

1. Intéressons-nous à l’éolienne **W7** du parc **Contoso Plant 1**.  

    * **Action** : Mettez à jour la plage d’affichage sur **1/1/17 20:00 to 3/10/17 20:00 (UTC)**, ajoutez le capteur **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed**, puis affichez les valeurs obtenues.

       ![Démarrage rapide 1][1]

1. Dernièrement, Contoso a détecté un incendie sur l’éolienne **W7**. Voyons cela de plus près. Nous pouvons constater que le capteur d’alerte incendie a été activé au cours de l’incendie.

    * **Action** : Mettez à jour la plage d’affichage sur **3/9/17 20:00 to 3/10/17 20:00 (UTC)** et ajoutez le capteur **Safety System** > **FireAlert**.

      ![Démarrage rapide 2][2]

1. Voyons ce qu’il s’est passé d’autre au moment de l’incendie. La pression d’huile et les avertissements actifs ont présenté un pic juste avant l’incendie, mais il était déjà trop tard pour éviter l’incident.

    * **Action** : Ajoutez le capteur **Pitch System** > **HydraulicOilPressure** et le capteur **Pitch System** > **ActiveWarning**.

      ![Démarrage rapide 3][3]

1. Si nous effectuons un zoom arrière, nous pouvons voir qu’il y avait des signes annonciateurs de l’incendie. Les deux capteurs ont connu des fluctuations. L’incident s’était-il déjà produit auparavant ?

    * **Action** : Mettez à jour la plage d’affichage sur **2/24/17 20:00 to 3/10/17 20:00 (UTC)**.

      ![Démarrage rapide 4][4]

1. Si nous examinons les deux années de données complètes, nous découvrons qu’un incendie s’est déjà produit avec les mêmes signes. Ces données permettent d’élaborer des systèmes permettant de détecter les problèmes de ce type en amont.

    * **Action** : Mettez à jour la plage d’affichage sur **1/1/16 to 12/31/17** (toutes les données).

       ![Démarrage rapide 5][5]

### <a name="demo-step-2"></a>Étape 2 de la démonstration

1. D’autres problèmes sont plus subtils et plus difficiles à diagnostiquer. Time Series Insights propose diverses fonctionnalités pour faciliter le dépistage des problèmes délicats. Ici, nous pouvons constater l’existence d’une panne du capteur d’avertissement sur l’éolienne **W6** le **25/06**. Que s’est-il réellement passé ?

    * **Action** : Supprimez les capteurs actuels, mettez à jour la plage d’affichage sur **6/1/17 20:00 to 7/1/17 20:00 (UTC)**, puis ajoutez le capteur **Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning**.

       ![Démarrage rapide 6][6]

1. L’avertissement indique un problème au niveau de la tension émise par la génératrice. Mais quelle en est la cause ? La puissance globale de sortie de la génératrice semble correcte sur les périodes courtes. Mais l’agrégation des données fait ressortir une baisse marquée.

    * **Action** : Supprimez le capteur **VoltageActuatorSwitchWarning**, ajoutez le capteur **Generator System** > **ActivePower**, puis mettez à jour l’intervalle sur **3d**.

       ![Démarrage rapide 7][7]

1. Si nous progressons dans le jeu de données, nous pouvons voir qu’il ne s’agit pas d’un problème passager, et qu’il est toujours d’actualité.

    * **Action** : Étirez l’intervalle de temps vers la droite.

       ![Démarrage rapide 8][8]

1. Approfondissons notre analyse. Nous pouvons ajouter d’autres points de données de capteur pour afficher la tension par phase. Cependant, tous les points de données sont similaires. Nous allons placer un marqueur pour voir les valeurs réelles. Il y a visiblement un problème au niveau de la sortie de la phase 3.

    * **Action** : Ajoutez les capteurs **Generator System** > **GridVoltagePhase1**, **GridVoltagePhase2** et **GridVoltagePhase3**. Placez un marqueur sur le dernier point de données dans la zone visible.

       ![Démarrage rapide 8][8]

1. Si nous affichons les trois points de données sur la même échelle, la baisse de la phase 3 est encore plus évidente. À ce stade, nous sommes prêts à soumettre le problème à l’équipe de maintenance bien en amont avec des informations sur la cause de l’avertissement.  

    * **Action** : Mettez à jour l’affichage pour superposer tous les capteurs sur la même échelle de graphique.

       ![Démarrage rapide 9][9]

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à créer votre propre environnement Time Series Insights (préversion) :

> [!div class="nextstepaction"]
> [Planifier votre environnement Time Series Insights (préversion)](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
