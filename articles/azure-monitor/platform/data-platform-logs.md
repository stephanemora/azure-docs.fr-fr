---
title: Journaux dans Azure Monitor | Microsoft Docs
description: Décrit les journaux qui sont utilisés dans Azure Monitor pour une analyse avancée des données de surveillance.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032766"
---
# <a name="azure-monitor-logs-overview"></a>Vue d’ensemble de Journaux d’activité Azure Monitor
Journaux d’activité Azure Monitor est une fonctionnalité d’Azure Monitor qui collecte et organise les données des journaux provenant de diverses sources et les met à disposition pour analyse à l’aide d’un langage d’interrogation sophistiqué. Les données provenant de différentes sources peuvent être consolidées dans un seul espace de travail et analysées ensemble pour effectuer des tâches telles que l’analyse des tendances, la création d’alertes et la visualisation.

## <a name="relationship-to-azure-monitor-metrics"></a>Relation avec Métriques Azure Monitor
Métriques Azure Monitor stocke les données numériques dans une base de données de série chronologique, ce qui les rend plus légères que les journaux d’activité d’Azure Monitor et capables de prendre en charge des scénarios en quasi-temps réel, ce qui les rend particulièrement utiles pour les alertes et la détection rapide des problèmes. Toutefois, la fonctionnalité Métriques peut uniquement stocker des données numériques dans une structure particulière, tandis que Journaux d’activité peut stocker différents types de données, chacun ayant sa propre structure. Vous pouvez également effectuer des analyses complexes sur les données de journaux à l’aide de requêtes de journal qui ne peuvent pas être utilisées pour l’analyse des données de métriques.

Les données numériques des sources de données sont souvent envoyées à la fonctionnalité Journaux d’activité en plus de Métriques. Bien qu’il existe des frais supplémentaires pour la collecte et la conservation de ces données dans Journaux d’activité, cela vous permet d’inclure des données de métriques dans les requêtes de journal et de les analyser avec vos autres données de surveillance.

## <a name="relationship-to-azure-data-explorer"></a>Relation avec Azure Data Explorer
La fonctionnalité Journaux d’activité Azure Monitor repose sur Azure Data Explorer. Un espace de travail Log Analytics est à peu près l’équivalent d’une base de données dans Azure Data Explorer, les tables sont structurées de la même façon, et les deux utilisent le même langage de requête Kusto (KQL). L’utilisation de Log Analytics pour travailler avec des requêtes Azure Monitor dans le portail Azure est similaire à l’utilisation de l’interface utilisateur web d’Azure Data Explorer. Vous pouvez même [inclure des données provenant d’un espace de travail Log Analytics dans une requête Azure Data Explorer](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Structure des données
Les données collectées par Journaux d’activité Azure Monitor sont stockées dans un [espace de travail Log Analytics](./design-logs-deployment.md) qui contient plusieurs tables qui stockent chacune des données provenant d’une source particulière. L’espace de travail définit l’emplacement géographique des données, les droits d’accès définissant quels utilisateurs peuvent accéder aux données et les paramètres de configuration tels que le niveau tarifaire et la conservation des données. Vous pouvez utiliser un espace de travail unique pour toutes vos données de surveillance ou créer plusieurs espaces de travail selon vos besoins. Pour plus d’informations sur la création de plusieurs espaces de travail, consultez [Conception de votre déploiement de journaux Azure Monitor](design-logs-deployment.md).

Chaque espace de travail contient plusieurs tables qui sont organisées en colonnes distinctes avec plusieurs lignes de données. Chaque table est définie par un ensemble unique de colonnes partagées par les lignes de données fournies par la source de données. 

[![Azure Monitor Logs structure](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Les données de journal d’Application Insights sont également stockées dans Journaux d’activité Azure Monitor, mais elles sont stockées différemment selon la configuration de votre application. Pour une application basée sur un espace de travail, les données sont stockées dans un espace de travail Log Analytics dans un ensemble standard de tables pour contenir des données telles que les requêtes d’application, les exceptions et les affichages de page. Plusieurs applications peuvent utiliser le même espace de travail. Pour une application classique, les données ne sont pas stockées dans un espace de travail Log Analytics. Elles utilisent le même langage de requête, et vous créez et exécutez des requêtes à l’aide du même outil Log Analytics dans le portail Azure. Toutefois, les données des applications classiques sont stockées séparément les unes des autres. Leur structure générale est la même que celle des applications basées sur un espace de travail, bien que les noms de table et de colonne soient différents. Pour une comparaison détaillée des deux types de stockage, consultez [Modifications des ressources basées sur un espace de travail](../app/apm-tables.md).


> [!NOTE]
> Nous proposons toujours une compatibilité descendante complète pour vos classeurs, alertes basées sur les journaux et requêtes de ressources Application Insights classiques au sein de l’expérience Application Insights. Pour interroger/afficher [le nouveau schéma ou la nouvelle structure de table basé sur un espace de travail](../app/apm-tables.md), vous devez d’abord accéder à votre espace de travail Log Analytics. Pendant la préversion, la sélection de **Journaux** dans les volets d’Application Insights vous donne accès à l’expérience de requête Application Insights classique. Consultez [Étendue de requête](../log-query/scope.md) pour plus d’informations.


[![Azure Monitor Logs structure for Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Requêtes dans les journaux
Les données sont extraites d’un espace de travail Log Analytics à l’aide d’une [requête de journal](../log-query/log-query-overview.md), qui est une requête en lecture seule visant à traiter les données et à retourner des résultats. Les requêtes de journal sont écrites en [KQL (Langage de requête Kusto)](/azure/data-explorer/kusto/query/), qui est le langage de requête utilisé par Azure Data Explorer. Utilisez Log Analytics, qui est un outil du portail Azure permettant de modifier et d’exécuter des requêtes de journal et d’analyser leurs résultats de manière interactive. Vous pouvez ensuite utiliser les requêtes que vous créez pour prendre en charge d’autres fonctionnalités dans Azure Monitor telles que les alertes et les classeurs de requêtes de journal.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Sources de données pour Journaux d’activité Azure Monitor
Azure Monitor collecte des données de journal à partir de diverses sources, notamment des ressources d’Azure Monitor et des agents fonctionnant sur des machines virtuelles. Pour obtenir la liste complète des sources de données qui envoient des données à un espace de travail Log Analytics, consultez [Quels sont les éléments supervisés par Azure Monitor ?](../monitor-reference.md)



## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [requêtes de journal](../log-query/log-query-overview.md) pour récupérer et analyser les données d’un espace de travail Log Analytics.
- Découvrez les [métriques dans Azure Monitor](data-platform-metrics.md).
- Découvrez les [données de surveillance disponibles](data-sources.md) pour différentes ressources dans Azure.

