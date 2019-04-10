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
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363036"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Pare-feu d’applications web Azure analyse et la journalisation 

Surveillance d’Azure web application firewall (WAF) et la journalisation sont fournis via l’intégration avec Azure Monitor et Azure Monitor et de journalisation des journaux.

> [!IMPORTANT]
> Le WAF surveillance et journalisation de fonctionnalité pour Azure porte d’entrée est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="azure-monitor"></a>Azure Monitor

Pare-feu d’applications Web avec les journaux principale est intégré avec [Azure Monitor](../azure-monitor/overview.md). Azure Monitor permet de suivre les informations de diagnostics, y compris les journaux et alertes WAF. Vous pouvez configurer la surveillance de pare-feu d’applications Web au sein de la ressource de la porte d’entrée dans le portail sous la **Diagnostics** onglet ou via l’analyse Azure service directement.

À partir du portail Azure, accédez au type de ressource porte d’entrée. À partir de **surveillance**/**métriques** onglet sur la gauche, vous pouvez ajouter **WebApplicationFirewallRequestCount** pour effectuer le suivi du nombre de demandes qui correspondent aux règles de pare-feu d’applications Web. Filtres personnalisés peuvent être créés en fonction des types d’action et les noms de règle.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Journaux et diagnostics

Pare-feu d’applications Web avec la porte d’entrée fournit des rapports détaillés sur chaque menace détectée. La journalisation est intégrée aux journaux Azure Diagnostics et les alertes sont enregistrées au format json. Ces journaux peuvent être intégrés aux [journaux Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

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

