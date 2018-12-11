---
title: Explorer l’environnement de démonstration Azure Time Series Insights (préversion) | Microsoft Docs
description: Comprendre l’environnement de démonstration Azure Time Series Insights (préversion)
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888772"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Explorer l’environnement de démonstration Azure Time Series Insights (préversion)

Ce guide de démarrage rapide vous montre comment démarrer avec l’Explorateur Azure Time Series Insights (TSI) dans un environnement de démonstration gratuit. Vous allez découvrir comment utiliser votre navigateur web pour visualiser de gros volumes de données IoT sectorielles historiques avant de faire le tour des fonctionnalités clés de l’explorateur Azure Time Series Insights (préversion).

Azure TSI propose une offre PaaS (platform-as-a-service) complète destinée à ingérer, traiter, stocker et interroger des données IoT hautement contextualisées et optimisées pour les séries chronologiques à des fins d’exploration de données ad hoc et d’analyse opérationnelle. Time Series Insights est une offre différenciée adaptée aux besoins uniques des déploiements IoT sectoriels.

L’environnement de démonstration met en lumière une société de production d’électricité, Contoso, qui utilise TSI pour rechercher dans ses données des insights exploitables et effectuer une brève analyse des causes premières des problèmes. Contoso exploite deux parcs éoliens qui se composent chacun de 10 éoliennes, chacune dotée de 20 capteurs qui transmettent des données toutes les minutes à Azure IoT Hub. Les capteurs recueillent des informations sur les conditions météorologiques, l’angle de calage et de lacet des pales, les performances de la génératrice, le comportement du multiplicateur et les moniteurs de sécurité.

TSI (préversion) est utilisé pour analyser les jeux de données toujours plus volumineux des deux dernières années – actuellement d’une taille de 40 Go – pour mieux comprendre et prédire les défaillances critiques et les problèmes de maintenance lents.

Si vous n’avez pas d’abonnement Azure, créez un  [compte Azure gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  avant de commencer.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Découvrir l’Explorateur Time Series Insights dans un environnement de démonstration

1. Dans votre navigateur, accédez à [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Si vous y êtes invité, connectez-vous à l’explorateur TSI à l’aide des informations d’identification de votre compte Azure.

### <a name="demo-step-one"></a>Étape 1 de la démonstration

1. Intéressons-nous à l’**éolienne n° 7 du parc éolien n° 1**.  

    * **Action** : mettre à jour la plage d’affichage sur `1/1/17 20:00 – 3/10/17 20:00 (UTC)` et ajouter le capteur `Farm 1 > W7 > Generator > GeneratorSpeed`. Afficher ensuite les valeurs obtenues.

       ![Démarrage rapide 1][1]

1. Dernièrement, **Contoso a détecté un incendie sur l’éolienne n° 7**. Voyons cela de plus près. Nous pouvons constater que le capteur d’alerte incendie s’est activé sur la période où a eu lieu l’incendie.

    * **Action** : mettre à jour la plage d’affichage sur `3/9/17 20:00 – 3/10/17 20:00 (UTC)` et ajouter le capteur `Safety > FireAlert`.

      ![Démarrage rapide 2][2]

1. Voyons ce qu’il s’est passé d’autre au moment de l’incendie. La pression d’huile et les avertissements actifs ont présenté un pic juste avant l’incendie, mais il était déjà trop tard pour éviter le problème.

    * **Action** : ajouter la capteur `Pitch > HydraulicOilPressure` et le capteur `Pitch > ActiveWarning`.

      ![Démarrage rapide 3][3]

1. Si nous effectuons un zoom arrière, nous pouvons voir qu’il y avait des signes annonciateurs de l’incendie. Les deux capteurs ont connu des fluctuations. Est-ce que cela s’est produit auparavant ?

    * **Action** : mettre à jour la plage d’affichage sur `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Démarrage rapide 4][4]

1. Si nous examinons les deux années de données complètes, nous découvrons qu’un incendie s’est déjà produit avec les mêmes signes. Ces données permettent d’élaborer des systèmes permettant de détecter les problèmes de ce type à un stade avancé.

    * **Action** : mettre à jour la plage d’affichage sur `1/1/16 – 12/31/17` (toutes les données).

       ![Démarrage rapide 5][5]

### <a name="demo-step-two"></a>Étape 2 de la démonstration

1. D’autres problèmes sont plus subtils et plus difficiles à diagnostiquer. Time Series Insights propose diverses fonctionnalités pour faciliter le dépistage des problèmes délicats. Ici, nous pouvons constater l’existence d’une panne du capteur d’avertissement sur `turbine #6` le `6/25`. Que s’est-il réellement passé ?

    * **Action** : supprimer les capteurs actuels. Mettre ensuite à jour la plage d’affichage sur `6/1/17 20:00 – 7/1/17 20:00 (UTC)` et ajouter `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Démarrage rapide 6][6]

1. L’avertissement indique un problème au niveau de la tension émise par la génératrice. Mais quelle en est la cause ? La puissance globale de sortie de la génératrice semble correcte sur les périodes courtes. Mais l’agrégation des données fait ressortir une baisse marquée.

    * **Action** : supprimer `VoltageActuatorSwitchWarning`, ajouter `Generator > ActivePower` et mettre à jour l’intervalle à `3d`.

       ![Démarrage rapide 7][7]

1. Si nous progressons dans le jeu de données, nous pouvons voir qu’il ne s’agit pas seulement d’un problème passager. Le problème se poursuit.

    * **Action** : étirer l’intervalle de temps vers la droite.

       ![Démarrage rapide 8][8]

1. Approfondissons notre analyse. Nous pouvons afficher d’autres points de données de capteur pour examiner la tension par phase. Mais ils sont tous comparables. Nous allons placer un marqueur pour voir les valeurs réelles. Il y a visiblement un problème au niveau de la sortie de la phase 3.

    * **Action** : `Add Generator > GridVoltagePhase1, 2, & 3`. Placer un marqueur sur le dernier point de données dans la zone visible.

       ![Démarrage rapide 8][8]

1. Si nous affichons les trois sur la même échelle, la baisse de la phase 3 est encore plus évidente. À ce stade, nous sommes prêts à soumettre ce problème à l’équipe de maintenance bien en amont avec des informations sur la cause de l’avertissement.  

    * **Action** : mettre à jour l’affichage pour superposer tous les capteurs sur la même échelle de graphique.

       ![Démarrage rapide 9][9]

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à créer votre propre environnement Azure TSI (préversion ):

> [!div class="nextstepaction"]
> [Planifier votre environnement Azure TSI (préversion)](time-series-insights-update-plan.md)

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