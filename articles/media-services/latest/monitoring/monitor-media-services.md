---
title: Surveillance de Media Services
description: Commencez ici pour découvrir comment surveiller Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609041"
---
# <a name="monitor-media-services"></a>Surveiller Media Services

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de supervision générées par Media Services et la façon dont vous pouvez utiliser les fonctionnalités d’Azure Monitor pour analyser ces données et créer des alertes.

## <a name="metrics-are-useful"></a>Utilité des métriques

Voici quelques exemples de la façon dont la surveillance de métriques Media Services peut vous aider à comprendre le fonctionnement de vos applications. Voici quelques questions qui peuvent être traitées avec les métriques Media Services :

- Comment surveiller mon point de terminaison de streaming standard pour savoir quand j’ai dépassé les limites ?
- Comment savoir si j’ai suffisamment d’unités d’échelle de point de terminaison de streaming Premium ?
- Comment définir une alerte pour savoir quand mettre à l’échelle mes points de terminaison de streaming ?
- Comment définir une alerte pour savoir quand la sortie maximale configurée sur le compte a été atteinte ?
- Comment voir la répartition des demandes qui échouent et ce qui est à l’origine de l’échec ?
- Comment voir combien de demandes TLS ou DASH sont extraites du packager ?
- Comment définir une alerte pour savoir quand la valeur seuil du nombre de requêtes ayant échoué a été atteinte ?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?

Media Services crée des données de supervision avec [Azure Monitor](../../../azure-monitor/overview.md), un service de supervision de pile complète dans Azure qui fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure en plus des ressources figurant dans d’autres clouds et localement.

Commencez par lire l’article [Supervision de ressources Azure avec Azure Monitor](../../../azure-monitor/essentials/monitor-azure-resource.md), qui décrit les concepts suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

## <a name="monitoring-data"></a>Données de surveillance

Media Services collecte les mêmes types de données de supervision que d’autres ressources Azure, lesquelles sont décrites dans [Supervision des données de ressources Azure](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Toutes les données collectées par Azure Monitor font partie d’un des deux types fondamentaux : métriques et journaux. Avec ces deux types, vous pouvez :

- Visualiser et analyser les données métriques à l’aide de Metrics Explorer.
- Surveiller les journaux de diagnostic Media Services et créer des alertes et des notifications à leur sujet.
- Avec les journaux, vous pouvez :
  - Les envoyer vers Stockage Azure
  - Les diffuser en continu vers Azure Event Hubs
  - Les exporter vers Log Analytics
  - Utiliser des services tiers

Pour plus d’informations sur les métriques et les journaux des métriques créés par Media Services, consultez l’article [Référence sur la surveillance des données de Media Services](monitor-media-services-data-reference.md).

## <a name="collection-and-routing"></a>Collecte et routage

Les *métriques de plateforme* et le *journal d’activité* sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les *journaux de ressources* ne sont **pas** collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez l’article [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../../../azure-monitor/essentials/diagnostic-settings.md).

Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour Media Services sont répertoriées dans [Référence sur la surveillance des données de Media Services](monitor-media-services-data-reference.md).

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les métriques de Media Services avec les métriques d’autres services Azure à l’aide de Metrics Explorer en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../../../azure-monitor/essentials/metrics-getting-started.md).

Pour obtenir la liste des métriques collectées pour Media Services, consultez [Référence sur la surveillance des données de Media Services](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

Le schéma des journaux de ressources de Media Services est disponible dans [Référence sur la surveillance des données de Media Services](monitor-media-services-data-reference.md).

Le [journal d’activité](../../../azure-monitor/essentials/activity-log.md) est un journal de plateforme dans Azure qui fournit des insights de tous les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.

Pour obtenir la liste des types de journaux de ressources collectés pour Media Services, consultez [Référence sur la surveillance des données de Media Services](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Pourquoi utiliser les journaux de diagnostic ?

Voici quelques éléments que vous pouvez examiner avec les journaux de diagnostic :

- Le nombre de licences fournies par type de DRM.
- Le nombre de licences fournies par stratégie.
- Les erreurs par type de DRM ou de stratégie.
- Le nombre de demandes de licence non autorisées émises par les clients.

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../../../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../../../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../../../azure-monitor/alerts/activity-log-alerts.md).

Les métriques de Media Services sont collectées à intervalles réguliers, que la valeur change ou non. Elles sont utiles pour les alertes, car elles peuvent être échantillonnées fréquemment. Une alerte peut être déclenchée rapidement avec une logique relativement simple.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
