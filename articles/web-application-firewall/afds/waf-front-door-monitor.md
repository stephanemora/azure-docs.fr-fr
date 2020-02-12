---
title: Surveillance et journalisation du pare-feu d’applications web Azure
description: Découvrez le pare-feu d’applications web (WAF) avec la surveillance et la journalisation FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 3446df6effd85a07beb463c1caa40c5826a9e019
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934709"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Surveillance et journalisation du pare-feu d’applications web Azure 

La surveillance et la journalisation du pare-feu d’applications web (WAF) Azure sont fournies via la journalisation et l’intégration avec Azure Monitor et les journaux Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

La journalisation WAF avec Front Door est intégrée à [Azure Monitor](../../azure-monitor/overview.md). Azure Monitor vous permet d’effectuer le suivi des informations de diagnostic, y compris des alertes et des journaux WAF. Vous pouvez configurer la supervision WAF dans la ressource Front Door sur le portail sous l’onglet **Diagnostics** ou directement via le service Azure Monitor.

À partir du portail Azure, accédez au type de ressource Front Door. Sous l’onglet **Supervision**/**Métriques** sur la gauche, ajoutez **WebApplicationFirewallRequestCount** pour suivre le nombre de requêtes qui correspondent à des règles WAF. Vous pouvez créer des filtres personnalisés sur la base des types d’action et des noms de règle.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Journaux et diagnostics

WAF avec Front Door fournit des rapports détaillés sur chaque menace détectée. La journalisation est intégrée aux journaux Diagnostics Azure et les alertes sont enregistrées au format json. Ces journaux d’activité peuvent être intégrés aux [journaux d’activité Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog consigne toutes les requêtes qui sont transmises aux back-ends clients. FrontdoorWebApplicationFirewallLog consigne toutes les requêtes qui correspondent à une règle WAF.

L’exemple de requête suivant obtient les journaux WAF sur les requêtes bloquées :

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Voici un exemple de requête enregistrée dans le journal WAF :

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

L’exemple de requête suivant obtient les entrées AccessLogs :

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Voici un exemple de requête enregistrée sans le journal d'accès :

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Front Door](../../frontdoor/front-door-overview.md).