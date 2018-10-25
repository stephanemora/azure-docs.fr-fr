---
title: Azure Monitor Metrics Explorer
description: En savoir plus sur les nouvelles fonctionnalités d’Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: f82b4dff20e2b26e62889c41b3ff3c27bc86066a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901411"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor Metrics Explorer

Azure Monitor Metrics Explorer est un composant du portail Microsoft Azure qui permet de tracer des graphiques, de corréler visuellement des tendances et d’investiguer sur les pics et les creux des valeurs de métriques. Metrics Explorer est un point de départ essentiel pour l’examen des différents problèmes de performances et de disponibilité avec vos applications et infrastructure hébergées dans Azure ou surveillées par les services d’Azure Monitor. 

## <a name="what-are-metrics-in-azure"></a>Que sont les métriques dans Azure ?

Les métriques dans Microsoft Azure sont les séries de valeurs et de comptes mesurés qui sont collectées et stockées au fil du temps. Il existe des métriques standard (ou de « plateforme ») et des métriques personnalisées. Les métriques standards vous sont fournies par la plateforme Azure elle-même. Les métriques standards reflètent les statistiques d’intégrité et d’utilisation de vos ressources Azure. Tandis que des métriques personnalisées sont envoyés à Azure par vos applications à l’aide de l’[API Application Insights pour les événements personnalisés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Les métriques personnalisées sont stockées dans les ressources d’Application Insights avec d’autres métriques spécifiques d’application.

## <a name="how-do-i-create-a-new-chart"></a>Comment créer un graphique ?

1. Ouvrez le portail Azure
2. Accédez au nouvel onglet **Moniteur**, puis sélectionnez **Métriques**.

   ![Image de métriques](./media/monitoring-metric-charts/0001.png)

3. Le **sélecteur de métriques** est ouvert automatiquement pour vous. Choisissez une ressource dans la liste pour afficher les métriques qui y sont associées. Seules les ressources auxquelles des métriques sont associées figurent dans la liste.

   ![Image de métriques](./media/monitoring-metric-charts/0002.png)

   > [!NOTE]
   >Si vous avez plus d’un abonnement Azure, Metrics Explorer extrait les ressources de tous les abonnements sélectionnés dans la liste Paramètres du portail -> Filtrer par abonnements. Pour modifier cela, cliquez sur l’icône d’engrenage des paramètres du portail en haut de l’écran, puis sélectionnez les abonnements à utiliser.

4. Pour certains types de ressources (comptes de stockage et machines virtuelles), avant de sélectionner une métrique, vous devez choisir un **espace de noms**. Chaque espace de noms inclut son propre ensemble de métriques pertinentes uniquement pour cet espace de noms et pas pour d’autres.

   Par exemple, chaque Stockage Azure a des métriques pour les sous-services « Objets blob », « Fichiers », « Files d’attente » et « Tables », qui font tous partie du compte de stockage. Toutefois, la métrique « Nombre de messages en file d’attente » est naturellement applicable au sous-service « File d’attente » et non à d’autres sous-services du compte de stockage.

   ![Image de métriques](./media/monitoring-metric-charts/0003.png)

5. Sélectionnez une métrique dans la liste. Si vous connaissez une partie du nom de la métrique souhaitée, vous pouvez commencer à la taper pour afficher une liste filtrée des métriques disponibles :

   ![Image de métriques](./media/monitoring-metric-charts/0004.png)

6. Après sélection d’une métrique, le graphique est rendu avec l’agrégation par défaut pour la métrique sélectionnée. À ce stade, vous pouvez simplement cliquer à côté du **sélecteur de métriques** pour fermer celui-ci. Vous pouvez également basculer le graphique vers une autre agrégation. Pour certaines métriques, un basculement d’agrégation permet de choisir la valeur que vous souhaitez afficher sur le graphique. Par exemple, vous pouvez basculer entre les valeurs moyennes, minimales et maximales. 

7. En cliquant sur l’icône Ajouter une métrique ![icône de métrique](./media/monitoring-metric-charts/icon001.png) et en répétant les étapes 3 à 6, vous pouvez ajouter plusieurs métriques sur le même graphique.

   > [!NOTE]
   > Vous ne souhaiterez normalement pas avoir des métriques avec des unités de mesure différentes (par exemple, « millisecondes » et « Ko ») ou d’échelles très différentes sur un même graphique. Au lieu de cela, envisagez d’utiliser plusieurs graphiques. Pour créer plusieurs graphiques dans Metrics Explorer, cliquez sur le bouton Ajouter un graphique.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Comment appliquer des filtres aux graphiques ?

Vous pouvez appliquer des filtres à des graphiques qui montrent des métriques associées à des dimensions. Par exemple, si la métrique « Nombre de transactions » a une dimension « Type de réponse » indiquant si la réponse provient de transactions qui ont réussi ou échoué, un filtrage sur cette dimension trace une courbe de graphique uniquement pour les transactions qui ont réussi ou échoué. 

### <a name="to-add-a-filter"></a>Pour ajouter un filtre :

1. Cliquez sur l’icône Ajouter un filtre ![icône de filtre](./media/monitoring-metric-charts/icon002.png) au-dessus du graphique.

2. Sélectionnez la dimension (propriété) que vous souhaitez filtrer.

   ![image de métrique](./media/monitoring-metric-charts/0006.png)

3. Sélectionnez les valeurs de dimension que vous souhaitez inclure lors du traçage du graphique (cet exemple illustre le filtrage des transactions de stockage qui ont réussi) :

   ![image de métrique](./media/monitoring-metric-charts/0007.png)

4. Après avoir sélectionné les valeurs de filtre, cliquez à côté du sélecteur de filtre pour fermer celui-ci. Le graphique montre à présent le nombre de transactions de stockage qui ont échoué :

   ![image de métrique](./media/monitoring-metric-charts/0008.png)

5. Vous pouvez répéter les étapes 1 à 4 pour appliquer plusieurs filtres aux mêmes graphiques.

## <a name="how-do-i-segment-a-chart"></a>Comment segmenter un graphique ?

Vous pouvez fractionner une métrique par dimension afin de visualiser la façon dont les différents segments de la métrique se comparent entre eux, et d’identifier des segments éloignés d’une dimension. 

### <a name="to-segment-a-chart"></a>Pour segmenter un graphique :

1. Cliquez sur l’icône Ajouter un regroupement  ![image de métrique](./media/monitoring-metric-charts/icon003.png) au-dessus du graphique.
 
   > [!NOTE]
   > Vous pouvez avoir plusieurs filtres mais un seul regroupement sur un graphique.

2. Choisissez la dimension sur laquelle vous souhaitez segmenter votre graphique : 

   ![image de métrique](./media/monitoring-metric-charts/0010.png)

   Le graphique montre à présent plusieurs lignes, une par segment de la dimension :

   ![image de métrique](./media/monitoring-metric-charts/0012.png)

3. Cliquez à côté du **sélecteur de regroupement** pour fermer celui-ci.

   > [!NOTE]
   > Pour masquer les segments non pertinents pour votre scénario et faciliter la lecture des graphiques, utilisez un Filtrage et un Regroupement sur la même dimension.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Comment épingler des graphiques à des tableaux de bord ?

Après avoir configuré les graphiques, vous pouvez les ajouter aux tableaux de bord afin de pouvoir les réafficher, par exemple, dans le contexte d’une autre télémétrie de surveillance ou d’un partage avec votre équipe. 

Pour épingler un graphique configuré à un tableau de bord :

Après avoir configuré votre graphique, cliquez sur le menu **Actions** dans l’angle supérieur droit du graphique, puis sur **Épingler au tableau de bord**.

   ![image de métrique](./media/monitoring-metric-charts/0013.png)

## <a name="next-steps"></a>Étapes suivantes

  Pour découvrir les meilleures pratiques en matière de création de tableaux de bord actionnables avec des métriques, voir [Création de tableaux de bord d’indicateurs de performance clés personnalisés](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards).