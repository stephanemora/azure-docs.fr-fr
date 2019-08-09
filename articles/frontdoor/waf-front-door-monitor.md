---
title: Supervision et journalisation du pare-feu d’applications web Azure
description: Découvrez les fonctionnalités de supervision et de journalisation du pare-feu d’applications web (WAF)
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: e4ba6cca679ce4910ea941d9578939721514b2ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478972"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Supervision et journalisation du pare-feu d’applications web Azure 

Les fonctionnalités de supervision et de journalisation du pare-feu d’applications web (WAF) Azure sont fournies via la journalisation et l’intégration avec Azure Monitor et les journaux Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

La journalisation WAF avec Front Door est intégrée à [Azure Monitor](../azure-monitor/overview.md). Azure Monitor vous permet d’effectuer le suivi des informations de diagnostic, y compris des alertes et des journaux WAF. Vous pouvez configurer la supervision WAF dans la ressource Front Door sur le portail sous l’onglet **Diagnostics** ou directement via le service Azure Monitor.

À partir du portail Azure, accédez au type de ressource Front Door. Sous l’onglet **Supervision**/**Métriques** sur la gauche, ajoutez **WebApplicationFirewallRequestCount** pour suivre le nombre de requêtes qui correspondent à des règles WAF. Vous pouvez créer des filtres personnalisés sur la base des types d’action et des noms de règle.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Journaux et diagnostics

WAF avec Front Door fournit des rapports détaillés sur chaque menace détectée. La journalisation est intégrée aux journaux Diagnostics Azure et les alertes sont enregistrées au format json. Ces journaux d’activité peuvent être intégrés aux [journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog consigne toutes les requêtes qui sont transmises aux back-ends clients. FrontdoorWebApplicationFirewallLog consigne toutes les requêtes qui correspondent à une règle WAF.

L’exemple de requête suivant obtient les journaux WAF sur les requêtes bloquées :

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

L’exemple de requête suivant obtient les entrées AccessLogs :

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Front Door](front-door-overview.md).

