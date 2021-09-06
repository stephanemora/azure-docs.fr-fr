---
title: Supervision d’Azure Application Gateway
description: Commencer ici pour découvrir comment superviser Azure Application Gateway
author: vhorne
ms.author: victorh
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 7789590a2d13e6811e0ad8e7d2fa53cea9e0069a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295982"
---
<!-- VERSION 2.2
Template for the main monitoring article for Azure services. 
Keep the required sections and add/modify any content for any information specific to your service. 
This article should be in your TOC with the name *monitor-[Azure Application Gateway].md* and the TOC title "Monitor Azure Application Gateway". 
Put accompanying reference information into an article in the Reference section of your TOC with the name *monitor-[service-name]-reference.md* and the TOC title "Monitoring data". 
Keep the headings in this order. 
-->

# <a name="monitoring-azure-application-gateway"></a>Supervision d’Azure Application Gateway
<!-- REQUIRED. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Add to it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. 

Cet article décrit les données de supervision générées par Azure Application Gateway. Azure Application Gateway utilise [Azure Monitor](../azure-monitor/overview.md). Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure qui l’utilisent, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).


<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact robb@microsoft.com -->

## <a name="monitoring-overview-page-in-azure-portal"></a>Page de présentation de la supervision dans Portail Azure
<!-- OPTIONAL. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Edit it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

La page de **présentation** du Portail Azure, inclut les mesures suivantes pour chaque Application Gateway :

- Somme du total des requêtes
- Somme des requêtes ayant échoué
- Somme des états de réponse par HttpStatus
- Somme du débit
- Somme des connexions actives (CurrentConnections)
- Nombre moy. d’hôtes sains par HttpSettings de BackendPool
- Nombre moy. d’hôtes non sains par HttpSettings de BackendPool

Il s’agit simplement d’un sous-ensemble des métriques disponibles pour Application Gateway. Pour obtenir davantage d’informations, voir [Informations de référence sur les données de supervision d’Azure Application Gateway](monitor-application-gateway-reference.md).


## <a name="azure-monitor-network-insights"></a>Azure Monitor Network Insights

<!-- OPTIONAL SECTION.  Only include if your service has an "insight" associated with it. Examples of insights include
  - CosmosDB https://docs.microsoft.com/azure/azure-monitor/insights/cosmosdb-insights-overview
  - If you still aren't sure, contact azmondocs@microsoft.com.>
-->

Dans Azure, certains services disposent d’un tableau de bord de supervision prédéfini spécial dans le Portail Azure qui constitue un point de départ pour la supervision de votre service. Ces tableaux de bord spéciaux sont appelés « Insights ».

<!-- Give a quick outline of what your "insight page" provides and refer to another article that gives details -->

Azure Monitor Network Insights offre une vue complète de l’intégrité et des métriques de toutes les ressources réseau déployées (y compris Application Gateway), sans aucune configuration nécessaire. Pour plus d’informations, consultez [Azure Monitor Network Insights](../azure-monitor/insights/network-insights-overview.md).

## <a name="monitoring-data"></a>Données de surveillance 

<!-- REQUIRED. Please keep headings in this order -->
Azure Application Gateway collecte les mêmes types de données de surveillance que d’autres ressources Azure, lesquelles sont décrites dans [Données de surveillance de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Pour obtenir des informations détaillées sur les métriques et les métriques de journaux créées par Azure Application Gateway, consultez [Informations de référence sur les données de supervision d’Azure Application Gateway](monitor-application-gateway-reference.md).

<!-- If your service has additional non-Azure Monitor monitoring data then outline and refer to that here. Also include that information in the data reference as appropriate. -->

## <a name="collection-and-routing"></a>Collecte et routage

<!-- REQUIRED. Please keep headings in this order -->

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

<!-- Include any additional information on collecting logs.  The number of things that diagnostics settings control is expanding -->

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories associées à Azure Application Gateway sont répertoriées dans [Informations de référence sur les données de supervision d’Azure Application Gateway](monitor-application-gateway-reference.md#resource-logs).

<!-- OPTIONAL: Add specific examples of configuration for this service. For example, CLI and PowerShell commands for creating diagnostic setting. Ideally, customers should set up a policy to automatically turn on collection for services. Azure monitor has Resource Manager template examples you can point to. See https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings.  Contact azmondocs@microsoft.com if you have questions.   -->

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

<!-- REQUIRED. Please keep headings in this order 
If you don't support metrics, say so. Some services may be only onboarded to logs -->

Vous pouvez analyser les métriques d’Azure Application Gateway avec les métriques d’autres services Azure à l’aide de Metrics Explorer en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

<!-- Point to the list of metrics available in your monitor-service-reference article. -->
Pour obtenir la liste des métriques de plateforme collectées pour Azure Application Gateway, consultez [Informations de référence sur les métriques de des données de supervision d’Application Gateway](monitor-application-gateway-reference.md#metrics).  


Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about how to use metrics explorer specifically for your service. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

<!-- REQUIRED. Please keep headings in this order
If you don't support resource logs, say so. Some services may be only onboarded to metrics and the activity log. -->

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est présenté dans [Schéma commun et propres aux services pour les journaux de ressources Azure](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). 

Le [journal d’activité](../azure-monitor/essentials/activity-log.md) est un journal de plateforme dans Azure qui fournit des insights sur les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.  

Pour obtenir la liste des types de journaux de ressources collectés pour Azure Application Gateway, consultez [Informations de référence sur les données de supervision d’Azure Application Gateway](monitor-application-gateway-reference.md#resource-logs).

Pour obtenir la liste des tables utilisées par Journaux d’activité Azure Monitor et interrogeables par Log Analytics, consultez [Informations de référence sur les données de surveillance d’Azure Application Gateway](monitor-application-gateway-reference.md#azure-monitor-logs-tables).  

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about log usage or what logs are most important. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

<!-- REQUIRED if you support logs. Please keep headings in this order -->
<!-- Add sample Log Analytics Kusto queries for your service. -->

> [!IMPORTANT]
> Lorsque vous sélectionnez **Journaux d’activité** dans le menu Application Gateway, Log Analytics s’ouvre avec l’étendue de requête définie sur la ressource Application Gateway actuelle. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres Application Gateways ou d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

<!-- REQUIRED: Include queries that are helpful for figuring out the health and state of your service. Ideally, use some of these queries in the alerts section. It's possible that some of your queries may be in the Log Analytics UI (sample or example queries). Check if so.  -->

Vous pouvez utiliser les requêtes suivantes pour vous aider à superviser votre ressource Application Gateway. 

<!-- Put in a code section here. -->  
```Kusto
// Requests per hour 
// Count of the incoming requests on the Application Gateway. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart 
```

```kusto
// Failed requests per hour 
// Count of requests to which Application Gateway responded with an error. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart
```

```kusto
// Top 10 Client IPs 
// Count of requests per client IP. 
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by clientIP_s
| top 10 by AggregatedValue
```

```kusto
// Errors by user agent 
// Number of errors by user agent. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by userAgent_s, _ResourceId
| sort by AggregatedValue desc
```

## <a name="alerts"></a>Alertes

<!-- SUGGESTED: Include useful alerts on metrics, logs, log conditions or activity log. Ask your PMs if you don't know. 
This information is the BIGGEST request we get in Azure Monitor so do not avoid it long term. People don't know what to monitor for best results. Be prescriptive  
-->

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients

<!-- only include next line if applications run on your service and work with App Insights. --> 

Si vous créez ou exécutez une application qui utilise Application Gateway, [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) peut vous proposer des types d’alertes supplémentaires.
<!-- end -->

Les tableaux suivants répertorient les règles d’alerte courantes et recommandées pour Application Gateway.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->

**Application Gateway v1**

| Type d’alerte | Condition | Description  |
|:---|:---|:---|
|Métrique|L’utilisation du processeur dépasse 80 %|Dans des conditions normales, l’utilisation du processeur ne doit pas dépasser 90 %, car cela peut entraîner une latence dans les sites Web hébergés derrière Application Gateway et perturber l’expérience du client.|
|Métrique|Le nombre d’hôtes non sains dépasse le seuil|Indique le nombre de serveurs principaux qu’Application Gateway ne peut pas détecter correctement. Cela permet de détecter les problèmes où les instances d’Application Gateway ne peuvent pas se connecter au serveur principal. Alerter si ce nombre dépasse 20 % de la capacité du back-end.|
|Métrique|L’état de réponse (4xx, 5xx) dépasse le seuil|Lorsque l’état de réponse d’Application Gateway est 4xx ou 5xx. Des réponses 4xx ou 5xx peuvent parfois s’afficher en raison de problèmes temporaires. Vous devez observer la passerelle en production pour déterminer le seuil statique, ou utiliser le seuil dynamique pour l’alerte.|
|Métrique|Si les échecs de requêtes franchissent le seuil|Lorsque la métrique des échecs de requêtes franchit un seuil. Vous devez observer la passerelle en production pour déterminer le seuil statique, ou utiliser le seuil dynamique pour l’alerte.|


**Application Gateway v2**

| Type d’alerte | Condition | Description  |
|:---|:---|:---|
|Métrique|Si l’utilisation de l’unité Compute dépasse 75 % de l’utilisation moyenne|L’unité de calcul mesure l’utilisation du calcul de votre Application Gateway. Vérifiez l’utilisation moyenne des unités de calcul au cours du dernier mois et définissez une alerte si elle dépasse 75 % de cette valeur.|
|Métrique|Si l’utilisation de l’unité de capacité dépasse 75 % de l’utilisation maximale|Les unités de capacité représentent l’utilisation globale de la passerelle en termes de débit, de calcul et de nombre de connexions. Vérifiez l’utilisation de votre unité de capacité maximale au cours du dernier mois et définissez une alerte si elle dépasse 75 % de cette valeur.|
|Métrique|Le nombre d’hôtes non sains dépasse le seuil|Indique le nombre de serveurs principaux qu’Application Gateway ne peut pas détecter correctement. Cela permet de détecter les problèmes où les instances de passerelle d’application ne peuvent pas se connecter au back-end. Alerter si ce nombre dépasse 20 % de la capacité du back-end.|
|Métrique|L’état de réponse (4xx, 5xx) dépasse le seuil|Lorsque l’état de réponse d’Application Gateway est 4xx ou 5xx. Des réponses 4xx ou 5xx peuvent parfois s’afficher en raison de problèmes temporaires. Vous devez observer la passerelle en production pour déterminer le seuil statique, ou utiliser le seuil dynamique pour l’alerte.|
|Métrique|Si les échecs de requêtes franchissent le seuil|Lorsque la métrique des échecs de requêtes franchit le seuil. Vous devez observer la passerelle en production pour déterminer le seuil statique, ou utiliser le seuil dynamique pour l’alerte.|
|Métrique|Le temps de réponse du dernier octet principal franchit le seuil|Indique l’intervalle de temps entre le début de l’établissement d’une connexion au serveur principal et la réception du dernier octet du corps de la réponse. Créez une alerte si la latence de la réponse du back-end est supérieure à celle d’un seuil normal.|
|Métrique|Si le temps total d’Application Gateway dépasse le seuil|Il s’agit de l’intervalle entre le moment où Application Gateway reçoit le premier octet de la requête HTTP et le moment où le dernier octet de la réponse a été envoyé au client. Doit créer alerte si la latence de la réponse du back-end est supérieure à celle d’un seuil normal.|

## <a name="next-steps"></a>Étapes suivantes

<!-- Add additional links. You can change the wording of these and add more if useful.   -->

- Pour plus d’informations sur les métriques, les journaux et d’autres valeurs importantes créées par Application Gateway, consultez [Informations de référence sur les données de supervision d’Application Gateway](monitor-application-gateway-reference.md).

- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).