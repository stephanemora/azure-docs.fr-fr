---
title: Supervision de Time Series Insights | Microsoft Docs
description: Supervisez Time Series Insights pour connaître la disponibilité, les performances et le fonctionnement.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 3fc880e238c1c9f45c663975470a6ab57267a648
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879542"
---
# <a name="monitoring-time-series-insights"></a>Supervision de Time Series Insights

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de supervision générées par Time Series Insights et la façon dont vous pouvez utiliser les fonctionnalités d’Azure Monitor pour analyser ces données et créer des alertes sur celles-ci.

## <a name="monitor-overview"></a>Présentation de Monitor

La page **Vue d’ensemble** du portail Azure pour chaque environnement Time Series Insights comprend un bref aperçu de l’utilisation des ressources, comme le nombre de messages reçus et le nombre d’octets stockés. Ces informations sont utiles. Toutefois, elles ne constituent qu’une petite quantité des données de supervision disponibles dans ce volet. Certaines de ces données sont collectées automatiquement et peuvent être analysées dès que vous créez la ressource. Vous pouvez activer d’autres types de collecte de données avec une certaine configuration.

## <a name="what-is-azure-monitor"></a>Présentation d’Azure Monitor

Time Series Insights crée des données de supervision avec [Azure Monitor](../azure-monitor/overview.md), un service de supervision de pile complète dans Azure qui fournit un ensemble complet de fonctionnalités permettant de superviser vos ressources Azure en plus des ressources figurant dans d’autres clouds et localement.

Commencez avec l’article [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md), qui décrit les concepts suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées pour Azure Time Series Insights. Ces sections fournissent également des exemples de configuration de la collecte de données et d’analyse de ces données à l’aide des outils Azure.

> [!TIP]
> Pour comprendre les coûts associés à Azure Monitor, consultez [Utilisation et estimation des coûts](../azure-monitor/platform/usage-estimated-costs.md). Pour comprendre le temps nécessaire à l’affichage de vos données dans Azure Monitor, consultez [Durée d’ingestion des données de journal](../azure-monitor/platform/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Supervision de données à partir d’Azure Time Series Insights

Azure Time Series Insights collecte les mêmes types de données de supervision que d’autres ressources Azure, lesquelles sont décrites dans [Supervision des données de ressources Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Consultez [Informations de référence sur les données de supervision d’Azure Time Series Insights](how-to-monitor-tsi-reference.md) pour obtenir des informations de référence détaillées des journaux et des métriques qu’il est possible de collecter.

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme sont collectées et stockées automatiquement, mais elles peuvent être routées vers d’autres emplacements à l’aide d’un paramètre de diagnostic.

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.
Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/platform/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter.

Vous pouvez collecter des journaux à partir des catégories suivantes pour Azure Time Series Insights :

   | Category | Description |
   |---|---|
   | Entrée  | La catégorie Ingress (Entrée) effectue le suivi des erreurs qui se produisent dans le pipeline d’entrée. Cette catégorie comprend les erreurs qui se produisent lors de la réception d’événements (comme les échecs de connexion à une source d’événement) et le traitement d’événements (comme les erreurs survenant lors de l’analyse d’une charge utile d’événement). |

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les métriques d’Azure Time Series Insights, ainsi que celles d’autres services Azure, en ouvrant Métriques dans le menu Azure Monitor. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md).

Pour obtenir la liste des métriques de plateforme collectées, consultez [Supervision des informations de référence sur les données Azure Time Series Insights](how-to-monitor-tsi-reference.md#metrics)

Cet exemple montre le nombre d’octets reçus de toutes les sources d’événements dans votre environnement Azure Time Series Insights.

**Octets reçus en entrée** [![Octets reçus en entrée par Azure Time Series Insights](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

L’exemple montre le nombre d’octets traités avec succès et disponibles pour la requête dans votre environnement Azure Time Series Insights.

**Octets stockés en entrée** [![Octets stockés en entrée par Azure Time Series Insights](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Analyse des journaux d’activité
Vous pouvez accéder aux journaux des ressources en tant qu’objet blob dans un compte de stockage, en tant que données d’événement ou par le biais de requêtes Log Analytics.

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Pour obtenir la liste des types de journaux de ressources collectés pour Azure Time Series Insights, consultez [Informations de référence sur les données de supervision d’Azure Time Series Insights](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights stocke des données dans les tables suivantes.

| Table de charge de travail | Description |
|:---|:---|
| TSIIngress | Table qui stocke les données de la catégorie Ingress (Entrée). La catégorie Ingress (Entrée) effectue le suivi des erreurs qui se produisent dans le pipeline d’entrée. Cette catégorie comprend les erreurs qui se produisent lors de la réception d’événements (comme les échecs de connexion à une source d’événement) et le traitement d’événements (comme les erreurs survenant lors de l’analyse d’une charge utile d’événement).

Pour router des données vers Azure Monitor Logs, vous devez créer un paramètre de diagnostic pour envoyer les journaux de ressources ou les métriques de plateforme à un espace de travail Log Analytics. Pour plus d’informations, consultez [Collecte et routage](../iot-hub/monitor-iot-hub.md#collection-and-routing).

## <a name="sample-queries"></a>Exemples de requêtes

Voici des requêtes que vous pouvez utiliser pour superviser plus facilement votre environnement Azure Time Series Insights :

+ Obtenir des informations détaillées sur les échecs de connexion à la source d’événements au cours des cinq derniers jours :

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Obtenir des informations détaillées sur les messages non valides reçus au cours des cinq derniers jours :

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/platform/alerts-metric-overview.md), sur des [journaux](../azure-monitor/platform/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/platform/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients.

Lors de la création d’une règle d’alerte basée sur des métriques de plateforme Time Series Insights qui sont collectées à l’unité, certaines agrégations peuvent ne pas être disponibles ou utilisables.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Informations de référence sur les données de supervision d’Azure Time Series Insights](how-to-monitor-tsi-reference.md) pour obtenir des informations de référence des journaux et des métriques créés par Azure Time Series Insights.
* Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).