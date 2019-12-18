---
title: Sources de données des classeurs Azure Monitor | Microsoft Docs
description: Créez des rapports complexes en toute simplicité grâce à des classeurs Azure Monitor paramétrables prédéfinis et personnalisés créés à partir de plusieurs sources de données.
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 368da2baaea1be41331d77f4dae1bdd9567b8cc5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872891"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Sources de données des classeurs Azure Monitor

Les classeurs sont compatibles avec de nombreuses sources de données. Cet article présente les sources de données actuellement disponibles pour les classeurs Azure Monitor.

## <a name="logs"></a>Journaux d’activité

Les classeurs permettent d’interroger les journaux provenant des sources suivantes :

* journaux Azure Monitor (ressources Application Insights et espaces de travail Log Analytics) ;
* données centrées sur les ressources (journaux d’activité).

Les auteurs de classeurs peuvent utiliser des requêtes KQL qui transforment les données de ressource sous-jacentes pour sélectionner un jeu de résultats visualisable sous forme de texte, de graphiques ou de grilles.

![Capture d’écran de l’interface de rapport des journaux des classeurs](./media/workbooks-overview/logs.png)

Les auteurs de classeurs peuvent interroger plusieurs ressources, ce qui donne une expérience de création de rapports riche et entièrement unifiée.

## <a name="metrics"></a>Mesures

Les ressources Azure émettent des [métriques](data-platform-metrics.md) accessibles dans les classeurs, au moyen d’un contrôle spécialisé permettant de spécifier les ressources cibles, les métriques souhaitées et leur agrégation. Ces données peuvent ensuite être tracées dans des graphiques ou des grilles.

![Capture d’écran de graphiques de métriques de classeur concernant l’utilisation du processeur](./media/workbooks-overview/metrics-graph.png)

![Capture d’écran de l’interface de métriques du classeur](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Les classeurs prennent en charge l’interrogation des ressources et de leurs métadonnées avec Azure Resource Graph (ARG). Cette fonctionnalité sert principalement à créer des étendues de requêtes personnalisées pour les rapports. L’étendue de ressource est exprimée par un sous-ensemble KQL pris en charge par ARG, ce qui est souvent suffisant pour les cas d’usage courants.

Pour qu’un contrôle de requête utilise cette source de données, utilisez la liste déroulante Type de requête pour choisir Azure Resource Graph et sélectionnez les abonnements à cibler. Utilisez le contrôle de requête pour ajouter le sous-ensemble KQL ARG qui sélectionne un sous-ensemble de ressources intéressant.


![Capture d’écran de la requête KQL Azure Resource Graph](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Alertes (préversion)

Les classeurs permettent aux utilisateurs de visualiser les alertes actives liées à leurs ressources. Cette fonctionnalité permet de réunir les données de notification (alerte) et les informations de diagnostic (métriques, journaux) dans un même rapport. Ces informations peuvent également être rassemblées pour créer des rapports enrichis qui combinent des insights sur ces différentes sources de données.

Pour qu’un contrôle de requête utilise cette source de données, utilisez la liste déroulante Type de requête pour choisir Alertes et sélectionnez les abonnements, groupes de ressources ou ressources à cibler. Utilisez les listes déroulantes de filtrage des alertes pour sélectionner un sous-ensemble d’alertes intéressant pour vos besoins d’analytique.

![Capture d’écran de la requête Alertes](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Intégrité de la charge de travail (préversion)

Azure Monitor possède des fonctionnalités proactives de monitorage de la disponibilité et des performances des systèmes d’exploitation invités Windows ou Linux. Azure Monitor modélise les composants clés et leurs relations, les critères de mesure de leur intégrité et les composants qui donnent lieu à des alertes en cas de détection d’un état défectueux. Les classeurs permettent aux utilisateurs d’utiliser ces informations pour créer des rapports interactifs enrichis.

Pour qu’un contrôle de requête utilise cette source de données, utilisez la liste déroulante **Type de requête** pour choisir Intégrité des charges de travail et sélectionnez l’abonnement, le groupe de ressources ou les ressources de machine virtuelle à cibler. Utilisez les listes déroulantes de filtrage de l’intégrité pour sélectionner un sous-ensemble d’incidents d’intégrité intéressant pour vos besoins d’analytique.

![Capture d’écran de la requête Alertes](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure Resource Health 

Les classeurs permettent de récupérer l’intégrité des ressources Azure et de la combiner avec d’autres sources de données pour créer des rapports d’intégrité enrichis et interactifs.

Pour qu’un contrôle de requête utilise cette source de données, utilisez la liste déroulante **Type de requête** pour choisir Intégrité Azure et sélectionnez les ressources à cibler. Utilisez les listes déroulantes de filtrage de l’intégrité pour sélectionner un sous-ensemble de problèmes de ressources intéressant pour vos besoins d’analytique.

![Capture d’écran de la requête Alertes](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (préversion)

Les classeurs prennent maintenant en charge l’interrogation de clusters [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) avec le puissant langage de requête [Kusto](https://docs.microsoft.com/azure/kusto/query/index).   

![Capture d’écran de la fenêtre de requête Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Étapes suivantes

* [Commencez](workbooks-visualizations.md) à en apprendre davantage sur les nombreuses options pour les visualisations enrichies des classeurs.
* [Contrôlez](workbooks-access-control.md) et partagez l’accès à vos ressources de classeur.
