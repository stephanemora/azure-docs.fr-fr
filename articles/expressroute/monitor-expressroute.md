---
title: Supervision d’Azure ExpressRoute
description: Commencez ici pour découvrir comment superviser Azure ExpressRoute.
services: expressroute
author: duongau
ms.author: duau
ms.service: expressroute
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: 2e54166f71071a21b84e5f58e27c4ebc8a7df03a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587855"
---
# <a name="monitoring-azure-expressroute"></a>Supervision d’Azure ExpressRoute

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. 

Cet article décrit les données de supervision générées par Azure ExpressRoute. Azure ExpressRoute utilise [Azure Monitor](../azure-monitor/overview.md). Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure qui l’utilisent, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
 
## <a name="expressroute-insights"></a>Insights ExpressRoute

Dans Azure, certains services disposent d’un tableau de bord de supervision prédéfini spécial dans le portail Azure qui constitue un point de départ pour la supervision de votre service. Ces tableaux de bord spéciaux sont appelés « insights ».

ExpressRoute utilise des insights réseau pour fournir un mappage de topologie détaillé de tous les composants ExpressRoute (peerings, connexions, passerelles) les uns par rapport aux autres. Les insights réseau pour ExpressRoute ont également un tableau de bord de métriques préchargé pour la disponibilité, le débit, les suppressions de paquets et les métriques de passerelle. Pour plus d’informations, consultez [Insight Azure ExpressRoute avec des insights réseau](expressroute-network-insights.md).

## <a name="monitoring-data"></a>Données de surveillance 

Azure ExpressRoute collecte les mêmes types de données de supervision que d’autres ressources Azure, lesquelles sont décrites dans [Données de supervision de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Pour obtenir des informations détaillées sur les métriques et les journaux de métriques créés par Azure ExpressRoute, consultez [Informations de référence sur les données de supervision d’Azure ExpressRoute](monitor-expressroute-reference.md).

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour *Azure ExpressRoute* sont listées dans [Informations de référence sur les données de supervision d’Azure ExpressRoute](monitor-expressroute-reference.md#resource-logs).

> [!IMPORTANT]
> L’activation de ces paramètres nécessite des services Azure supplémentaires (compte de stockage, hub d’événements ou Log Analytics), ce qui peut augmenter vos coûts. Pour calculer un coût estimé, consultez la rubrique [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les métriques d’*Azure ExpressRoute* avec des métriques d’autres services Azure à l’aide de Metrics Explorer en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="Capture d’écran du tableau de bord des métriques pour ExpressRoute.":::

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

* Pour voir les métriques **ExpressRoute**, filtrez les *circuits ExpressRoute* par type de ressource. 
* Pour afficher les métriques **Global Reach**, filtrez les *circuits ExpressRoute* par type de ressource, puis sélectionnez une ressource de circuit ExpressRoute où Global Reach est activé. 
* Pour afficher les métriques **ExpressRoute Direct**, filtrez les *ports ExpressRoute* par type de ressource. 

Une fois qu’une métrique est sélectionnée, l’agrégation par défaut est appliquée. Si vous le souhaitez, vous pouvez appliquer une division pour afficher les métriques avec différentes dimensions.

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Le schéma des journaux de ressources ExpressRoute se trouve dans les [Informations de référence sur les données Azure ExpressRoute](monitor-expressroute-reference.md#schemas). 

Le [journal d’activité](../azure-monitor/essentials/activity-log.md) est une journalisation de plateforme qui fournit des insights sur les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.

ExpressRoute stocke les données dans les tables suivantes.

| Table de charge de travail | Description |
| ----- | ----------- |
| AzureDiagnostics | Table commune utilisée par plusieurs services pour stocker les journaux de ressources. Les journaux de ressources d’ExpressRoute peuvent être identifiés avec `MICROSOFT.NETWORK`. |
| AzureMetrics | Données de métriques émises par ExpressRoute et qui mesurent leur intégrité et leurs performances. 

Pour afficher ces tables, accédez à votre ressource de circuit ExpressRoute, sélectionnez **Journaux** sous *Supervision*.

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

Voici quelques requêtes que vous pouvez entrer dans la barre Recherche dans les journaux pour vous permettre de superviser plus facilement vos ressources Azure ExpressRoute. Ces requêtes fonctionnent avec le [nouveau langage](../azure-monitor/logs/log-query-overview.md).

* Pour interroger une table de route BGP découverte au cours des 12 dernières heures.

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceType , network_s, path_s, OperationName
    ```

* Pour interroger les messages d’information BGP par niveau, par type de ressource et par réseau.

    ```Kusto
    AzureDiagnostics
    | where Level == "Informational"
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceId , Level, ResourceType , network_s, path_s
    ```

* Pour interroger le graphe de trafic BitInPerSeconds au cours de la dernière heure.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsInPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* Pour interroger le graphe de trafic BitOutPerSeconds au cours de la dernière heure.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsOutPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* Pour interroger le graphe ArpAvailability par intervalles de 5 minutes.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "ArpAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

* Pour interroger le graphe de disponibilité du protocole BGP par intervalles de 5 minutes.

    ```Kusto
    AzureMetrics
    | where MetricName == "BGPAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients.

Le tableau suivant liste les règles d’alerte courantes et recommandées pour ExpressRoute.

| Type d’alerte | Condition | Description  |
|:---|:---|:---|
| Inactivité de la disponibilité du protocole ARP | Nom de la dimension : Type de peering, Type d’agrégation : Avg, Opérateur : Inférieur à, Valeur de seuil : 100 % | Quand la disponibilité du protocole ARP est inactive pour un type de peering. |
| Inactivité de la disponibilité du protocole BGP | Nom de la dimension : Pair, Type d’agrégation : Avg, Opérateur : Inférieur à, Valeur de seuil : 100 % | Quand la disponibilité du protocole BGP est inactive pour un pair. |

### <a name="alerts-for-expressroute-gateway-connections"></a>Alertes pour les connexions de passerelle ExpressRoute

1. Pour configurer des alertes, accédez à **Azure Monitor**, puis sélectionnez **Alertes**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Sélectionnez **+ Sélectionner une cible**, puis sélectionnez la ressource de connexion de passerelle ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="cible":::
3. Définissez les détails de l’alerte.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="groupe d’actions":::
4. Définissez et ajoutez le groupe d’actions.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="ajouter un groupe d'actions":::


### <a name="alerts-based-on-each-peering"></a>Alertes basées sur chaque peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="chaque peering":::

### <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurer des alertes pour les journaux d’activité sur des circuits

Dans les **Critères d’alerte**, vous pouvez sélectionner le Type de signal **Journal d’activité** puis sélectionner le Signal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="journaux d'activité":::

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des informations de référence sur les métriques, les journaux et d’autres valeurs importantes créées par ExpressRoute, consultez [Informations de référence sur la supervision des données ExpressRoute](monitor-expressroute-reference.md).
* Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/overview.md).
