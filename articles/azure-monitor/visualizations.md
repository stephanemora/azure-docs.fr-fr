---
title: Visualisation des données à partir d’Azure Monitor | Microsoft Docs
description: Récapitule les méthodes disponibles pour visualiser les données stockées dans Azure Monitor, y compris les données du magasin de métriques et de Log Analytics.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: 4200accee94840a8a4455308f24a857fd5d7034a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632974"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualisation des données à partir d’Azure Monitor
Cet article récapitule les méthodes disponibles pour visualiser les données stockées dans Azure Monitor. Celles-ci incluent les [métriques dans le magasin de métriques Azure](../azure-monitor/platform/data-collection.md#metrics) et les [données du journal dans Log Analytics](../azure-monitor/platform/data-collection.md#logs). 

Les visualisations telles que les diagrammes ou graphiques peuvent vous aider à analyser vos données de supervision pour explorer des problèmes en profondeur et identifier des modèles. En fonction de l’outil que vous utilisez, vous pouvez également avoir la possibilité de partager les visualisations avec d’autres utilisateurs à l’intérieur et en dehors de votre organisation.

## <a name="azure-dashboards"></a>Tableaux de bord Azure
Les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md) constituent la principale technologie de création de tableaux de bord pour Azure. Ils sont particulièrement efficaces pour fournir un panorama unique de vos infrastructure et services Azure, ce qui vous permet d’identifier rapidement les problèmes importants.

![tableau de bord](media/visualizations/dashboard.png)

### <a name="advantages"></a>Avantages
- Intégration approfondie à Azure. Les visualisations peuvent être épinglées aux tableaux de bord à partir de plusieurs pages Azure, notamment Metrics Explorer, Log Analytics et Application Insights.
- Prend en charge les métriques et les journaux.
- Combinez des données provenant de plusieurs sources, notamment une sortie [Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md), les [requêtes Log Analytics](../log-analytics/log-analytics-queries.md), ainsi que les [cartes](../application-insights/app-insights-app-map.md) et les [données de disponibilité]() dans Application Insights.
- Possibilité de définir des tableaux de bord personnels ou partagés. Intégré à [l’authentification basée sur les rôles (RBAC)](../role-based-access-control/overview.md) Azure.
- Actualisation automatique. L’actualisation des métriques a lieu selon un intervalle de temps de cinq minutes au minimum. Les journaux sont actualisés chaque minute.
- Les tableaux de bord de métriques sont paramétrés avec un horodatage et des paramètres personnalisés.
- Options de disposition flexibles.
- Mode plein écran.


### <a name="limitations"></a>Limites
- Contrôle limité des visualisations Log Analytics sans prise en charge des tables de données. Le nombre total de séries de données est limité à 10, les nouvelles séries de données étant regroupées sous un _autre_ compartiment.
- Aucune prise en charge de paramètre personnalisé pour les graphiques Log Analytics.
- Les graphiques Log Analytics sont limités aux 30 derniers jours.
- Les graphiques Log Analytics peuvent uniquement être épinglés à des tableaux de bord partagés.
- Aucune interactivité avec les données de tableau de bord.
- Exploration contextuelle limitée.

## <a name="azure-monitor-views"></a>Affichages Azure Monitor
Les [affichages dans Azure Monitor](../azure-monitor/platform/view-designer.md) vous permettent de créer des visualisations personnalisées avec les données de journal stockées dans Log Analytics. Les [solutions de supervision](../azure-monitor/insights/solutions.md) s’en servent pour présenter les données qu’elles collectent.

![Affichage](media/visualizations/view.png)

### <a name="advantages"></a>Avantages
- Visualisations riches des données Log Analytics.
- Possibilité d’exporter et d’importer des affichages pour les transférer vers d’autres groupes de ressources et abonnements.
- Intégration au modèle de gestion Log Analytics avec des espaces de travail et des solutions de supervision.
- [Filtres](../azure-monitor/platform/view-designer-filters.md) pour les paramètres personnalisés.
- Interactif ; prend en charge l’exploration sur plusieurs niveaux (d’un affichage à l’autre)

### <a name="limitations"></a>Limites
- Prend en charge les journaux, mais pas les métriques.
- Aucun affichage personnel. Disponible pour tous les utilisateurs ayant accès à l’espace de travail.
- Aucune actualisation automatique.
- Options de disposition limitées.
- Aucune prise en charge de l’interrogation de plusieurs espaces de travail Log Analytics et applications Application Insights.
- La taille de la réponse des requêtes est limitée à 8 Mo, tandis que le temps d’exécution des requêtes est limité à 110 secondes.



## <a name="application-insights-workbooks"></a>Classeurs Application Insights
Les [classeurs](../application-insights/app-insights-usage-workbooks.md) sont des documents interactifs qui fournissent des insights détaillés sur vos données, et favorisent l’investigation et la collaboration au sein de l’équipe. Parmi les exemples typiques d’utilisation des classeurs citons les guides de résolution des problèmes et les bilans d’incidents.

![Classeur](media/visualizations/workbook.png)

### <a name="advantages"></a>Avantages
- Prend en charge les métriques et les journaux.
- Prend en charge des paramètres qui permettent de créer des rapports interactifs où la sélection d’un élément dans une table met dynamiquement à jour les visualisations et graphiques associés.
- Flux de type document.
- Possibilité de définir des classeurs personnels ou partagés.
- Expérience de création simple et conviviale.
- Les modèles prennent en charge la galerie de modèles publique basée sur GitHub.

### <a name="limitations"></a>Limites
- Aucune actualisation automatique.
- Les classeurs présentant une disposition moins dense que les tableaux de bord, ils n’offrent pas la même vision panoramique. Conviennent davantage pour fournir des insights plus approfondis.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) est particulièrement utile pour créer des rapports et des tableaux de bord centrés sur l’entreprise, ainsi que des rapports d’analyse de tendances d’indicateur de performance clé à long terme. Vous pouvez [importer les résultats d’une requête Log Analytics](../log-analytics/log-analytics-powerbi.md) dans un jeu de données Power BI afin de bénéficier de ses fonctionnalités telles que la combinaison de données provenant de différentes sources et le partage de rapports sur le web et sur des appareils mobiles.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Avantages
- Visualisations riches.
- Interactivité extensive, notamment par le biais du zoom avant et du filtrage croisé.
- Partage aisé à travers votre organisation.
- Intégration aux autres données issues de plusieurs sources de données.
- Meilleures performances avec les résultats mis en cache dans un cube.


### <a name="limitations"></a>Limites
- Prend en charge les journaux, mais pas les métriques.
- Aucune intégration à Azure. Ne peut pas gérer les tableaux de bord et les modèles par le biais d’Azure Resource Manager.
- Les résultats des requêtes doivent être importés dans le modèle Power BI à configurer. L’actualisation et la taille des résultats sont soumises à des limites.
- L’actualisation des données est limitée à huit exécutions par jour.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) est une plateforme ouverte qui convient parfaitement dans les tableaux de bord opérationnels. Elle est particulièrement utile pour détecter, isoler et trier les incidents opérationnels. Vous pouvez ajouter le [plug-in de source de données Azure Monitor pour Grafana](../monitoring-and-diagnostics/monitor-send-to-grafana.md) à votre abonnement Azure afin qu’il visualise vos données de métriques Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Avantages
- Visualisations riches.
- Écosystème de sources de données riche.
- Interactivité des données, notamment par le biais du zoom avant.
- Prend en charge les paramètres.

### <a name="limitations"></a>Limites
- Prend en charge les métriques, mais pas les journaux.
- Aucune intégration à Azure. Ne peut pas gérer les tableaux de bord et les modèles par le biais d’Azure Resource Manager.
- Coût lié à la prise en charge d’une infrastructure Grafana supplémentaire ou coûts supplémentaires liés à Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Créer votre propre application personnalisée
Vous pouvez accéder aux données des métriques Azure et de Log Analytics via leur API à l’aide de n’importe quel client REST, et créer ainsi vos propres applications et sites web personnalisés.

### <a name="advantages"></a>Avantages
- Flexibilité complète de l’interface utilisateur, de la visualisation, de l’interactivité et des fonctionnalités.
- Possibilité de combiner les métriques et les données de journal avec d’autres sources de données.

### <a name="disadvantages"></a>Inconvénients
- Effort d’ingénierie significatif requis.


## <a name="next-steps"></a>Étapes suivantes
- Découvrez les [données collectées par Azure Monitor](../azure-monitor/platform/data-collection.md).
- Découvrez les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md).
- Découvrez les [affichages dans Azure Monitor](../azure-monitor/platform/view-designer.md).
- Découvrez les [classeurs dans Application Insights](../application-insights/app-insights-usage-workbooks.md).
- Découvrez [l’importation de données de journal dans Power BI](../log-analytics/log-analytics-powerbi.md).
- Découvrez le [plug-in de source de données Azure Monitor pour Grafana](../monitoring-and-diagnostics/monitor-send-to-grafana.md).
