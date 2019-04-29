---
title: Plateforme de données Azure Monitor | Microsoft Docs
description: La supervision des données collectées par Azure Monitor est divisée en plusieurs métriques légères et capables de prendre en charge des scénarios en temps quasi-réel ainsi que les journaux d’activité utilisés pour une analyse avancée.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808932"
---
# <a name="azure-monitor-data-platform"></a>Plateforme de données Azure Monitor

L’activation de l’observabilité entre les environnements informatiques complexes d’aujourd'hui en cours d’exécution des applications distribuées qui s’appuient sur le cloud et services sur site, de nécessite la collecte des données opérationnelles à partir de chaque couche et que chaque composant du système distribué. Vous devez être en mesure d’effectuer des informations détaillées sur ces données et de la consolider dans un volet unique avec différentes perspectives pour prendre en charge de la multitude des parties prenantes dans votre organisation.

[Azure Monitor](../overview.md) collecte et agrège les données à partir de diverses sources dans une plate-forme de données commune où il peut être utilisé pour l’analyse, visualisation et d’alerte. Il fournit une expérience cohérente sur des données provenant de plusieurs sources, qui vous donne des informations détaillées sur toutes vos ressources surveillés et même avec des données provenant d’autres services qui stockent leurs données dans Azure Monitor.


![Vue d’ensemble d’Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Données d’observation dans Azure Monitor
Métriques, journaux et traces distribuées sont communément appelé les trois piliers de la capacité d’observation. Voici les différents types de données qu’un outil de surveillance doit collecter et analyser pour fournir l’observabilité de suffisamment d’un système analysé. Observation peut être obtenue en corrélation des données à partir de plusieurs piliers et l’agrégation des données sur l’ensemble de ressources en cours d’analyse. Étant donné que Azure Monitor stocke des données à partir d’un ensemble de sources multiples, les données puissent être corrélées et analysées à l’aide d’un ensemble commun d’outils. Il met également en corrélation les données entre plusieurs abonnements Azure et les locataires, tout en hébergeant des données pour d’autres services.

Ressources Azure génèrent une quantité importante de données d’analyse. Azure Monitor consolide ces données, ainsi que la surveillance des données provenant d’autres sources dans un à une plateforme de métriques ou les journaux. Chacun est optimisé pour les scénarios de surveillance particuliers, et chacun prend en charge différentes fonctionnalités dans Azure Monitor. Fonctionnalités, telles que les données d’analyse, visualisations ou de génération d’alertes vous obligent à comprendre les différences afin que vous pouvez implémenter votre scénario le plus de manière efficace et à moindre coût. Insights dans Azure Monitor telles que [Application Insights](../app/app-insights-overview.md) ou [Azure Monitor pour les machines virtuelles](../insights/vminsights-overview.md) disposent d’outils d’analyse qui vous permettent de vous concentrer sur le scénario d’analyse spécifique sans avoir à comprendre le différences entre les deux types de données. 


### <a name="metrics"></a>Mesures
Les [métriques](data-platform-metrics.md) sont des valeurs numériques décrivant un aspect d’un système à un moment précis dans le temps. Ils sont collectés à intervalles réguliers et sont identifiés avec un horodatage, un nom, une valeur et une ou plusieurs étiquettes de définition. Métriques peuvent être agrégées à l’aide d’un éventail d’algorithmes, par rapport aux autres métriques et analyse des tendances au fil du temps. 

Mesures dans Azure Monitor sont stockés dans une base de données de série chronologique qui est optimisé pour l’analyse des données horodatées. Cela rend les métriques particulièrement adapté aux alertes et plus rapide la détection des problèmes. Ils peuvent vous indiquer comment votre système s’exécute, mais doivent généralement être combinées avec les journaux pour identifier la cause racine des problèmes.

Métriques sont disponibles pour effectuer une analyse interactive dans le portail Azure avec [Metrics Explorer](../app/metrics-explorer.md). Ils peuvent être ajoutés à un [tableau de bord Azure](../learn/tutorial-app-dashboards.md) pour la visualisation en combinaison avec d’autres données et utilisé pour quasiment en temps réel [d’alerte](alerts-metric.md).

En savoir plus sur les mesures Azure Monitor, y compris leurs sources de données dans [métriques dans Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Journaux d’activité
[Journaux](data-platform-logs.md) sont des événements qui se sont produites au sein du système. Ils peuvent contenir différents types de données et peuvent être structurés ou libérer le texte de forme avec un horodatage. Ils peuvent être créés par intermittence comme des événements dans l’environnement de génèrent des entrées de journal, et un système surchargé généralement générera plus volume de journal.

Dans Azure Monitor sont stockés dans un espace de travail Analytique de journal qui est basé sur [Explorateur de données Azure](/azure/data-explorer/) qui fournit un moteur d’analyse puissante et [langage de requête riche](/azure/kusto/query/). En général, les journaux fournissent suffisamment d’informations pour fournir un contexte complet du problème identifié et sont utiles pour identifier les cas de la racine des problèmes.

> [!NOTE]
> Il est important de faire la distinction entre les journaux d’Azure Monitor et les sources de données de journal dans Azure. Par exemple, les événements de niveau d’abonnement dans Azure sont écrits dans un [journal d’activité](activity-logs-overview.md) que vous pouvez afficher dans le menu Azure Monitor. La plupart des ressources seront consignent des informations opérationnelles pour un [journal de diagnostic](diagnostic-logs-overview.md) que vous pouvez transférer à différents emplacements. Journaux d’Azure Monitor est une plateforme de données de journal qui collecte des journaux d’activité et les journaux de diagnostic, ainsi que d’autres données d’analyse pour fournir une analyse approfondie sur votre ensemble de ressources.


 Vous pouvez travailler avec [enregistrer des requêtes](../log-query/log-query-overview.md) interactivement avec [Analytique de journal](../log-query/portals.md) dans le portail Azure ou ajouter les résultats à un [tableau de bord Azure](../learn/tutorial-app-dashboards.md) pour la visualisation en combinaison avec autres données. Vous pouvez également créer [alertes de journal](alerts-log.md) qui déclenchera une alerte basée sur les résultats d’une requête de planification.

En savoir plus sur les journaux d’Azure Monitor, y compris leurs sources de données dans [journaux dans Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Traces distribuées
Traces sont série d’événements associés qui suivent une demande de l’utilisateur via un système distribué. Ils peuvent servir à déterminer le comportement du code d’application et les performances des transactions différentes. Bien que les journaux seront souvent créés par les composants individuels d’un système distribué, une trace mesure l’opération et les performances de votre application sur l’ensemble des composants.

Traçage distribué dans Azure Monitor est activé avec le [SDK Application Insights](../app/distributed-tracing.md), et les données de trace sont stockées avec d’autres données de journal d’application collectées par Application Insights. Cela met à disposition les même outils d’analyse que d’autres données de journal, y compris des requêtes de journal, les tableaux de bord et les alertes.

En savoir plus sur traçage à distribué [What ' s traçage distribué ?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Comparer les journaux et métriques Azure Monitor

Le tableau suivant compare les métriques et les journaux dans Azure Monitor.

| Attribut  | Mesures | Journaux d’activité |
|:---|:---|:---|
| Avantages | Léger et capable de scénarios quasiment en temps réel telles que des alertes. Idéal pour la détection rapide des problèmes. | Analyser avec riche langage de requête. Idéal pour une analyse approfondie et identifier la cause racine. |
| Données | Valeurs numériques uniquement | Données numériques ou de texte |
| Structure | Ensemble standard de propriétés, y compris la durée échantillon, d’une ressource en cours d’analyse, d’une valeur numérique. Certaines mesures incluent plusieurs dimensions pour une définition plus précise. | Ensemble de propriétés en fonction du type de journal unique. |
| Collection | Collectées à intervalles réguliers. | Peuvent être collectées sporadique événements déclenchent un enregistrement à créer. |
| Afficher dans le portail Azure | Metrics Explorer | Log Analytics |
| Incluent des sources de données | Métriques de la plateforme collectées à partir de ressources Azure.<br>Applications analysées par Application Insights.<br>Personnalisé défini par l’API ou application. | Application et les journaux de diagnostic.<br>Solutions de surveillance.<br>Les agents et les extensions de machine virtuelle.<br>L’application demande et les exceptions.<br>Azure Security Center.<br>API du collecteur de données. |

## <a name="collect-monitoring-data"></a>Collecter les données de surveillance
Différents [des sources de données pour Azure Monitor](data-sources.md) écrira dans un espace de travail Analytique de journal (journaux) ou la base de données de métriques Azure Monitor (métriques) ou les deux. Certaines sources d’écrit directement dans ces magasins de données, tandis que d’autres peuvent écrire dans un autre emplacement, comme stockage Azure et nécessiter une configuration pour remplir les journaux ou métriques. 

Consultez [métriques dans Azure Monitor](data-platform-metrics.md) et [journaux dans Azure Monitor](data-platform-logs.md) pour une liste des différentes sources de données qui remplissent chaque type.


## <a name="stream-data-to-external-systems"></a>Transmettre des données en continu vers les systèmes externes
En plus d’utiliser les outils dans Azure pour analyser les données de supervision, vous aurez peut-être besoin de transférer ces données vers un outil externe, comme un produit SIEM (Security Information and Event Management). Ce transfert est le plus souvent effectué directement à partir des ressources supervisées au travers d’[Azure Event Hubs](/azure/event-hubs/). Certaines sources peuvent être configurés pour envoyer des données directement à un concentrateur d’événements pendant que vous pouvez utiliser un autre processus comme une application logique pour récupérer les données requises. Consultez [Azure Stream données d’analyse à un concentrateur d’événements pour la consommation par un outil externe](stream-monitoring-data-event-hubs.md) pour plus d’informations.



## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [métriques dans Azure Monitor](data-platform-metrics.md).
- En savoir plus sur [journaux dans Azure Monitor](data-platform-logs.md).
- Découvrez les [données de surveillance disponibles](data-sources.md) pour différentes ressources dans Azure.
