---
title: Unifier plusieurs ressources Application Insights Azure Monitor | Microsoft Docs
description: Cet article explique en détail comment utiliser une fonction dans les journaux d’activité Azure Monitor pour interroger plusieurs ressources Application Insights et visualiser ces données.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429514"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unifier plusieurs ressources Application Insights Azure Monitor 
Cet article décrit comment interroger et afficher toutes vos données du journal d’application Application Insights à partir d’un emplacement unique, même quand elles se trouvent dans différents abonnements Azure, en remplacement de la dépréciation d’Application Insights Connector. Le nombre de ressources Application Insights que vous pouvez inclure dans une seule requête est limité à 100.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Approche recommandée pour interroger plusieurs ressources Application Insights 
Lister plusieurs ressources Application Insights dans une requête peut être une opération fastidieuse et difficile à gérer. Au lieu de cela, vous pouvez utiliser une fonction pour séparer la logique de requête de l’étendue des applications.  

Cet exemple montre comment superviser plusieurs ressources Application Insights et visualiser le nombre de requêtes ayant échoué par nom d’application. Avant de commencer, exécutez cette requête dans l’espace de travail qui est connecté aux ressources Application Insights pour obtenir la liste des applications connectées : 

```
ApplicationInsights
| summarize by ApplicationName
```

Créez une fonction en utilisant l'opérateur union avec la liste des applications, puis enregistrez la requête en tant que fonction dans votre espace de travail avec l'alias *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Vous pouvez à tout moment modifier les applications répertoriées sur le portail en accédant à l'Explorateur de requêtes de votre espace de travail, en sélectionnant la fonction à modifier et en enregistrant. Vous pouvez également utiliser la cmdlet PowerShell `SavedSearch`. La commande `withsource= SourceApp` ajoute une colonne aux résultats, qui désigne l’application ayant envoyé le journal. 
>
>La requête utilise le schéma Application Insights, bien qu’elle soit exécutée dans l’espace de travail, car la fonction applicationsScoping retourne la structure de données Application Insights. 
>
>L’opérateur parse est facultatif dans cet exemple ; il extrait le nom de l’application à partir de la propriété SourceApp. 

Vous êtes maintenant prêt à utiliser la fonction applicationsScoping dans la requête interressource :  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

L’alias de fonction retourne l’union des requêtes à partir de toutes les applications définies. Ensuite, la requête filtre les demandes ayant échoué et permet de visualiser les tendances par application.

![Exemple de résultats de requête croisée](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Interroger des ressources Application Insights et des données d’espace de travail 
Quand vous arrêtez le connecteur et que vous devez effectuer des requêtes sur une plage de temps qui a été découpée par la conservation des données Application Insights (90 jours), vous devez effectuer des [requêtes interressources](../../azure-monitor/log-query/cross-workspace-query.md) sur l’espace de travail et les ressources Application Insights pour une période intermédiaire. C’est nécessaire jusqu’à ce que vos données d’applications s’accumulent conformément aux nouvelles exigences de conservation des données Application Insights mentionnées ci-dessus. La requête nécessite certaines manipulations, dans la mesure où les schémas dans Application Insights et l’espace de travail sont différents. Consultez le tableau, plus loin dans cette section, qui souligne les différences entre les schémas. 

>[!NOTE]
>Les [requêtes inter-ressources](../log-query/cross-workspace-query.md) des alertes de journal sont prises en charge par la nouvelle [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Par défaut, Azure Monitor utilise l'[API Alerte Log Analytics héritée](../platform/api-alerts.md) pour créer de nouvelles règles d'alerte de journal à partir du portail Azure, sauf si vous basculez depuis l'[API Alertes de journal héritée](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Après le basculement, la nouvelle API devient la valeur par défaut des nouvelles règles d'alerte du portail Azure et vous permet de créer des règles d'alertes de journal pour les requêtes inter-ressources. Vous pouvez créer règles d'alertes de journal pour les [requêtes inter-ressources](../log-query/cross-workspace-query.md) sans basculer en utilisant le [modèle ARM de l'API schededuRuRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template). Mais cette règle d'alerte est gérable via l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) et non à partir du portail Azure.

Par exemple, si le connecteur a cessé de fonctionner le 01-11-2018, quand vous interrogez les journaux d’activité pour des ressources Application Insights et des données d’applications dans l’espace de travail, votre requête sera construite comme dans l’exemple suivant :

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Différences entre les schémas d’espace de travail Log Analytics et Application Insights
Le tableau suivant montre les différences entre les schémas Log Analytics et Application Insights.  

| Propriétés d’espace de travail Log Analytics| Propriétés de ressources Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | Message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | Nom |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Ordinateur | cloud_RoleInstance | 
| Pays | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | Nom | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | Message | 
| ExceptionType | Type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| SE | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | Nom | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | Nom | 
| RequestSuccess | réussi | 
| ResponseCode | resultCode | 
| Rôle | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | Type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Étapes suivantes

Utilisez [Recherche dans les journaux](../../azure-monitor/log-query/log-query-overview.md) pour afficher des informations détaillées sur vos applications Application Insights.
