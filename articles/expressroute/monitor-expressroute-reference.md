---
title: Informations de référence sur la supervision des données ExpressRoute
description: Matériel de référence important nécessaire pour analyser ExpressRoute
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: cd5a4ef0196843069d50f333089f8bffca956acc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289800"
---
# <a name="monitoring-expressroute-data-reference"></a>Informations de référence sur la supervision des données ExpressRoute

Cet article fournit des informations de référence sur les données de journal et de métriques collectées pour analyser les performances et la disponibilité d’ExpressRoute.
Pour plus d’informations sur la collecte et l’analyse de données de supervision pour ExpressRoute, consultez [Supervision d’ExpressRoute](monitor-expressroute.md).

## <a name="metrics"></a>Mesures

Cette section liste toutes les métriques de plateforme collectées automatiquement pour ExpressRoute. Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

| Type de métrique | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Circuit ExpressRoute | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
| Peering de circuit ExpressRoute | [Microsoft.Network/expressRouteCircuits/peerings](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuitspeerings) |
| Passerelles ExpressRoute | [Microsoft.Network/expressRouteGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutegateways) |
| ExpressRoute Direct | [Microsoft.Network/expressRoutePorts](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |

>[!NOTE]
> L’utilisation des métriques *GlobalGlobalReachBitsInPerSecond* et *GlobalGlobalReachBitsOutPerSecond* sera visible uniquement si au moins une connexion Global Reach est établie.
>

## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

ExpressRoute a les dimensions suivantes associées à ses métriques.

### <a name="dimension-for-expressroute-circuit"></a>Dimension pour circuit ExpressRoute

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **PeeringType** | Type de peering configuré. Les valeurs prises en charge sont Microsoft et Peering privé. |
| **Peering** | Les valeurs prises en charge sont Primaire et Secondaire. |
| **PeeredCircuitSkey** | Clé de service du circuit ExpressRoute distant connectée à l’aide de Global Reach. |

### <a name="dimension-for-expressroute-gateway"></a>Dimension pour passerelle ExpressRoute

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **roleInstance** | Instance de passerelle. Chaque passerelle ExpressRoute est composée de plusieurs instances, et les valeurs prises en charge sont GatewayTenantWork_IN_X (où X est au minimum 0 et au maximal le nombre d’instances de passerelle -1). |

### <a name="dimension-for-express-direct"></a>Dimension pour ExpressRoute Direct

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **Lien** | Lien physique. Chaque paire de ports ExpressRoute Direct est composée de deux liens physiques pour la redondance, et les valeurs prises en charge sont link1 et link2. |

## <a name="resource-logs"></a>Journaux d’activité de ressources

Cette section liste les types de journaux de ressources que vous pouvez collecter pour ExpressRoute. 

|Type de journal de ressources | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Circuit ExpressRoute | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkexpressroutecircuits) |

Pour référence, consultez la liste de [tous les types de catégories de journaux de ressources pris en charge dans Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs

Azure ExpressRoute utilise des tables Kusto des journaux Azure Monitor. Vous pouvez interroger ces tables à l’aide de Log Analytics. Pour obtenir une référence de toutes les tables Azure Monitor Logs/Log Analytics, consultez la [référence relative aux tables Azure Monitor Logs](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="activity-log"></a>Journal d’activité

Le tableau suivant liste les opérations relatives à ExpressRoute qui peuvent être créées dans le journal d’activité.

| Opération | Description |
|:---|:---|
| Toutes les opérations d’administration | Toutes les opérations administratives, dont la création, la mise à jour et la suppression d’un circuit ExpressRoute. |
| Créer ou mettre à jour un circuit ExpressRoute | Un circuit ExpressRoute a été créé ou mis à jour. |
| Supprimer le circuit ExpressRoute | Un circuit ExpressRoute a été supprimé.|

Pour plus d’informations sur le schéma des entrées du journal d’activité, consultez [Schéma du journal d’activité](../azure-monitor/essentials/activity-log-schema.md).

## <a name="schemas"></a>Schémas

Pour une description détaillée du schéma général des journaux de diagnostic, consultez [Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure](../azure-monitor/essentials/resource-logs-schema.md).

Quand vous examinez des métriques par le biais de Log Analytics, la sortie contient les colonnes suivantes :

|**Colonne**|**Type**|**Description**|
| --- | --- | --- |
|TimeGrain|string|PT1M (les valeurs de métriques sont envoyées [push] toutes les minutes)|
|Count|real|Généralement égale à 2 (chaque MSEE envoie [push] une valeur métrique unique toutes les minutes)|
|Minimum|real|Minimum des deux valeurs de métriques envoyées (push) par les deux MSEE|
|Maximale|real|Maximum des deux valeurs de métriques envoyées (push) par les deux MSEE|
|Average|real|Égale à (Minimum + Maximum)/2|
|Total|real|Somme des deux valeurs de métriques des deux MSEE (principale valeur sur laquelle se concentrer pour la métrique interrogée)|

## <a name="see-also"></a>Voir aussi

- Pour obtenir une description de la supervision d’Azure ExpressRoute, consultez [Supervision d’Azure ExpressRoute](monitor-expressroute.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).