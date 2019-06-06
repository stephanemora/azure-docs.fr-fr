---
title: Pare-feu d’applications web Azure analyse et la journalisation
description: Découvrez le pare-feu d’applications web (WAF) avec principale surveillance et journalisation
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
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478972"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Pare-feu d’applications web Azure analyse et la journalisation 

Surveillance d’Azure web application firewall (WAF) et la journalisation sont fournis via l’intégration avec Azure Monitor et Azure Monitor et de journalisation des journaux.

## <a name="azure-monitor"></a>Azure Monitor

Pare-feu d’applications Web avec les journaux principale est intégré avec [Azure Monitor](../azure-monitor/overview.md). Azure Monitor permet de suivre les informations de diagnostics, y compris les journaux et alertes WAF. Vous pouvez configurer la surveillance de pare-feu d’applications Web au sein de la ressource de la porte d’entrée dans le portail sous la **Diagnostics** onglet ou via l’analyse Azure service directement.

À partir du portail Azure, accédez au type de ressource porte d’entrée. À partir de **surveillance**/**métriques** onglet sur la gauche, vous pouvez ajouter **WebApplicationFirewallRequestCount** pour effectuer le suivi du nombre de demandes qui correspondent aux règles de pare-feu d’applications Web. Filtres personnalisés peuvent être créés en fonction des types d’action et les noms de règle.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Journaux et diagnostics

Pare-feu d’applications Web avec la porte d’entrée fournit des rapports détaillés sur chaque menace détectée. La journalisation est intégrée aux journaux d’activité Azure Diagnostics et les alertes sont enregistrées au format json. Ces journaux d’activité peuvent être intégrés aux [journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog enregistre toutes les demandes qui sont transférés au client des serveurs principaux. FrontdoorWebApplicationFirewallLog enregistre toute demande qui correspond à une règle de pare-feu d’applications Web.

L’exemple de requête suivante obtient les journaux WAF sur les demandes bloquées :

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

L’exemple de requête suivante obtient les entrées AccessLogs :

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [porte d’entrée](front-door-overview.md).

