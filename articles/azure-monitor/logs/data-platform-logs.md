---
title: Journaux Azure Monitor
description: Découvrez les principes de base des journaux Azure Monitor qui sont utilisés pour l’analyse avancée des données de supervision.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 73fce933d81daca78d538b9a169a53d2a99c9869
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226995"
---
# <a name="azure-monitor-logs-overview"></a>Vue d’ensemble de Journaux d’activité Azure Monitor
Les journaux Azure Monitor sont une fonctionnalité Azure Monitor qui collecte et organise les données de performances et de journal issues de [ressources supervisées](../monitor-reference.md). Les données de plusieurs sources peuvent être consolidées dans un espace de travail unique. Ces sources comprennent :

- [Journaux de plateforme](../essentials/platform-logs-overview.md) des services Azure.
- Journal et données de performances des [agents de machine virtuelle](../agents/agents-overview.md).
- Utilisation et données de performances des [applications](../app/app-insights-overview.md). 

Vous pouvez ensuite analyser les données à l’aide d’un langage de requête sophistiqué qui est capable d’analyser rapidement des millions d’enregistrements. 

Vous pouvez effectuer une requête simple qui récupère un ensemble spécifique d’enregistrements ou effectuer une analyse de données sophistiquée pour identifier les modèles critiques dans vos données de supervision. Utilisez les requêtes de journal et leurs résultats de manière interactive grâce à Log Analytics, utilisez-les dans des règles d’alerte pour être informé des problèmes de manière proactive, ou visualisez leurs résultats dans un classeur ou un tableau de bord.

> [!NOTE]
> Les journaux Azure Monitor représentent la moitié de la plateforme de données qui prend en charge Azure Monitor. L’autre moitié, constituée des [métriques Azure Monitor](../essentials/data-platform-metrics.md), stocke les données numériques dans une base de données de série chronologique. Les données numériques sont plus légères que les données contenues dans les journaux Azure Monitor. Les métriques Azure Monitor peuvent prendre en charge des scénarios en quasi-temps réel, ce qui les rend utiles pour la génération d’alertes et la détection rapide des problèmes. 
>
> Les métriques Azure Monitor peuvent uniquement stocker des données numériques dans une structure particulière, tandis que les journaux Azure Monitor peuvent stocker divers types de données qui ont leurs propres structures. Vous pouvez également effectuer des analyses complexes sur les données de journaux Azure Monitor à l’aide de requêtes de journal, qui ne peuvent pas être utilisées pour l’analyse des données des métriques Azure Monitor.

## <a name="what-can-you-do-with-azure-monitor-logs"></a>Que pouvez-vous faire avec les journaux Azure Monitor ?
Le tableau suivant décrit certaines méthodes pour utiliser les journaux Azure Monitor :

|  | Description |
|:---|:---|
| **Analyser** | Utiliser [Log Analytics](./log-analytics-tutorial.md) dans le portail Azure pour écrire des [requêtes de journal](./log-query-overview.md) et analyser de manière interactive des données de journal à l’aide d’un puissant moteur d’analyse. |
| **Alert** | Configurer une [règle d’alerte de journal](../alerts/alerts-log.md) qui envoie une notification ou prend une [action de façon automatique](../alerts/action-groups.md) lorsque les résultats de la requête correspondent à un résultat spécifique. |
| **Visualiser** | Épingler les résultats de requête affichés sous forme de tableaux ou de graphiques dans un [tableau de bord Azure](../../azure-portal/azure-portal-dashboards.md).<br>Créer un [classeur](../visualize/workbooks-overview.md) à combiner avec plusieurs jeux de données dans un rapport interactif. <br>Exporter les résultats d’une requête vers [Power BI](./log-powerbi.md) pour utiliser différentes visualisations et les partager avec les utilisateurs en dehors d’Azure.<br>Exporter les résultats d’une requête vers [Grafana](../visualize/grafana-plugin.md) pour utiliser ses tableaux de bord et les combiner avec d’autres sources de données.|
| **Obtenir des informations** | Prendre en charge des [insights](../monitor-reference.md#insights-and-core-solutions) qui offrent une expérience de supervision personnalisée pour des applications et services spécifiques.  |
| **Récupérer** | Accédez aux résultats de requête de journal à partir d’une ligne de commande à l’aide de l’interface [Azure CLI](/cli/azure/monitor/log-analytics).<br>Accédez aux résultats de requête de journal à partir d’une ligne de commande à l’aide de [cmdlets PowerShell](/powershell/module/az.operationalinsights).<br>Accédez aux résultats de requête de journal à partir d’une application personnalisée à l’aide de [l’API REST](https://dev.loganalytics.io/). |
| **Export** | Configurez [l’exportation automatisée des données de journal](./logs-data-export.md) vers le compte de stockage Azure ou Azure Event Hubs.<br>Générez un flux de travail pour récupérer des données de journal et copiez-les vers un emplacement externe à l’aide [d’Azure Logic Apps](./logicapp-flow-connector.md). |

![Diagramme montrant une vue d’ensemble des journaux Azure Monitor.](media/data-platform-logs/logs-overview.png)

## <a name="data-collection"></a>Collecte de données
Une fois que vous avez créé un espace de travail Log Analytics, vous devez configurer des sources pour envoyer leurs données. Aucune donnée n’est collectée automatiquement. 

Cette configuration diffère d’une source de données à l’autre. Par exemple :

- [Créez des paramètres de diagnostic](../essentials/diagnostic-settings.md) pour envoyer des journaux de ressources à partir de ressources Azure vers l’espace de travail. 
- [Activer VM Insights](../vm/vminsights-enable-overview.md) pour collecter des données de machines virtuelles. 
- [Configurez des sources de données sur l’espace de travail](../agents/data-sources.md) pour collecter des événements et des données de performances supplémentaires.

Pour obtenir la liste complète des sources de données que vous pouvez configurer pour envoyer des données aux journaux Azure Monitor, consultez [Quels sont les éléments supervisés par Azure Monitor ?](../monitor-reference.md).

## <a name="log-analytics-and-workspaces"></a>Log Analytics et espaces de travail
Log Analytics est un outil du portail Azure. Vous pouvez l’utiliser pour modifier et exécuter des requêtes de journal, ainsi que pour analyser les résultats de manière interactive. Vous pouvez ensuite utiliser ces requêtes pour prendre en charge d’autres fonctionnalités dans Azure Monitor, comme les classeurs et les alertes de requête de journal. Accédez à Log Analytics à partir de l’option **Journaux** dans le menu Azure Monitor ou à partir de la plupart des autres services du portail Azure.

Pour obtenir une description de Log Analytics, consultez [Présentation de Log Analytics dans Azure Monitor](./log-analytics-overview.md). Pour parcourir les fonctionnalités de Log Analytics afin de créer une requête de journal simple et d’analyser les résultats, consultez [Tutoriel Log Analytics](./log-analytics-tutorial.md).

Les journaux Azure Monitor stockent les données dans un ou plusieurs [espaces de travail Log Analytics](./design-logs-deployment.md). Un espace de travail définit :

- Emplacement géographique des données.
- Droits d’accès qui définissent les utilisateurs pouvant accéder aux données.
- Paramètres de configuration, comme le niveau tarifaire et la conservation des données.  

Vous devez créer au moins un espace de travail pour utiliser les journaux Azure Monitor. Un espace de travail unique peut suffire pour toutes vos données de supervision ou vous pouvez choisir de créer plusieurs espaces de travail selon vos besoins. Par exemple, vous pouvez avoir un espace de travail pour vos données de production et un autre pour les tests. 

Pour créer un espace de travail, consultez [Créer un espace de travail Log Analytics dans le portail Azure](./quick-create-workspace.md). Pour plus d’informations sur la création de plusieurs espaces de travail, consultez [Conception de votre déploiement de journaux Azure Monitor](design-logs-deployment.md).

## <a name="log-queries"></a>Requêtes dans les journaux
Les données sont extraites d’un espace de travail Log Analytics à l’aide d’une requête de journal, qui est une requête en lecture seule visant à traiter les données et à retourner des résultats. Les requêtes sont écrites dans le [langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/). KQL est le même langage de requête que celui utilisé par Azure Data Explorer. 

Vous pouvez écrire des requêtes de journal dans Log Analytics pour analyser de manière interactive leurs résultats, les utiliser dans des règles d’alerte pour être informé de manière proactive des problèmes ou inclure leurs résultats dans des classeurs ou des tableaux de bord. Les insights incluent des requêtes prédéfinies pour la prise en charge de leurs vues et classeurs.

Pour obtenir une liste des emplacements où les requêtes de journal sont utilisées, ainsi que des références de tutoriels et autres documents pour vous aider à démarrer, consultez [Requêtes de journal dans Azure Monitor](./log-query-overview.md).

![Capture d’écran montrant les requêtes dans Log Analytics.](media/data-platform-logs/log-analytics.png)

## <a name="data-structure"></a>Structure de données
Les requêtes de journal récupèrent leurs données d’un espace de travail Log Analytics. Chaque espace de travail contient plusieurs tables qui sont organisées en colonnes distinctes avec plusieurs lignes de données. Chaque table est définie par un ensemble unique de colonnes. Les lignes de données fournies par la source de données partagent ces colonnes. 

[![Diagramme montrant la structure des journaux Azure Monitor.](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)

Les données de journal d’Application Insights sont également stockées dans Journaux d’activité Azure Monitor, mais elles sont stockées différemment selon la configuration de votre application : 

- Pour une application basée sur un espace de travail, les données sont stockées dans un espace de travail Log Analytics dans un ensemble standard de tables. Les types de données incluent les requêtes d’application, les exceptions et les affichages de page. Plusieurs applications peuvent utiliser le même espace de travail. 

- Pour une application classique, les données ne sont pas stockées dans un espace de travail Log Analytics. Les données utilisent le même langage de requête. Vous créez et exécutez des requêtes à l’aide du même outil Log Analytics dans le portail Azure. Les données des applications classiques sont stockées séparément les unes des autres. La structure générale est la même que celle des applications basées sur un espace de travail, bien que les noms de table et de colonne soient différents. 

Pour une comparaison détaillée du schéma des applications classiques et des applications basées sur l’espace de travail, consultez [Modifications des ressources basées sur un espace de travail](../app/apm-tables.md).

> [!NOTE]
> L’expérience classique Application Insights comprend la compatibilité descendante de vos requêtes de ressources, classeurs et alertes basées sur un journal. Pour interroger ou afficher [le nouveau schéma ou la nouvelle structure de table basée sur un espace de travail](../app/apm-tables.md), vous devez d’abord accéder à votre espace de travail Log Analytics. Pendant la préversion, la sélection de **Journaux** dans les volets d’Application Insights vous donne accès à l’expérience de requête Application Insights classique. Pour plus d’informations, consultez [Étendue de requête](./scope.md).

[![Diagramme montrant la structure des journaux Azure Monitor pour Application Insights.](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="relationship-to-azure-data-explorer"></a>Relation avec Azure Data Explorer
La fonctionnalité Journaux d’activité Azure Monitor repose sur Azure Data Explorer. Un espace de travail Log Analytics est à peu près l’équivalent d’une base de données dans Azure Data Explorer. Les tables sont structurées de la même façon. Elles utilisent toutes les deux le langage KQL. 

L’utilisation de Log Analytics pour travailler avec des requêtes Azure Monitor dans le portail Azure est similaire à l’utilisation de l’interface utilisateur web d’Azure Data Explorer. Vous pouvez même [inclure des données provenant d’un espace de travail Log Analytics dans une requête Azure Data Explorer](/azure/data-explorer/query-monitor-data). 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [requêtes de journal](./log-query-overview.md) pour récupérer et analyser les données d’un espace de travail Log Analytics.
- Découvrez les [métriques dans Azure Monitor](../essentials/data-platform-metrics.md).
- Découvrez les [données de surveillance disponibles](../agents/data-sources.md) pour différentes ressources dans Azure.