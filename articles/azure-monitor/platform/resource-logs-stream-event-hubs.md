---
title: Diffuser en continu les journaux de plateforme Azure vers un Event Hub
description: Découvrez comment envoyer en streaming des journaux de ressources à des hubs d’événements pour envoyer des données à des systèmes externes tels que des solutions SIEM tierces et d’autres solutions d’analytique des journaux.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658912"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Diffuser en continu les journaux de plateforme Azure vers Azure Event Hubs
[Les journaux de plateforme](platform-logs-overview.md) dans Azure, y compris le journal d’activité Azure et les journaux de ressources, fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent.  Cet article décrit la diffusion en continu des journaux d’activité de plateformes à des hubs d’événements pour envoyer des données à des systèmes externes tels que des solutions SIEM tierces et d’autres solutions d’analytique des journaux d’activité.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Comment utiliser les journaux d’activité de plateformes envoyés à un Event Hub
Diffusez les journaux d’activité de plateformes dans Azure vers Event hubs pour fournir les fonctionnalités suivantes :

* **Diffuser en continu les journaux d’activité vers des systèmes de journalisation et de télémétrie tiers** : diffusez tous vos journaux d’activité des plateformes vers un hub d’événements unique pour envoyer les données de journal d’activité vers un outil tiers SIEM ou Log Analytics.
  
* **Créer une plateforme de journalisation et de télémétrie personnalisée** : la nature hautement évolutive de l’abonnement aux event hubs vous permet d’ingérer de manière flexible les journaux d’activité de plateformes dans une plateforme de télémétrie personnalisée. Pour plus d’informations, consultez [Conception et dimensionnement d’une plateforme de télémétrie à échelle mondiale sur Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

* **Afficher l’état d’intégrité du service en diffusant des données vers Power BI** : utilisez Event Hubs, Stream Analytics et Power BI pour transformer vos données de diagnostic en informations en temps réel sur vos services Azure. Consultez [Stream Analytics et Power BI : tableau de bord d’analyse en temps réel pour les données de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) pour plus de détails sur cette solution.

    Le code SQL suivant est un exemple de requête Stream Analytics que vous pouvez utiliser pour analyser toutes les données de journal dans une table Power BI :
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Prérequis
Si vous n’en avez pas encore, vous devez [créer un hub d’événements](../../event-hubs/event-hubs-create.md). Si vous disposez déjà d’un paramètre de diagnostic à l’aide de cet espace de noms Event Hubs, ce hub d’événements sera réutilisé.

La stratégie d’accès partagé pour l’espace de noms définit les autorisations dont dispose le mécanisme de diffusion en continu. La diffusion en continu vers Event Hubs requiert des autorisations de gestion, d’envoi et d’écoute. Vous pouvez créer ou modifier les stratégies d’accès partagé dans le Portail Microsoft Azure sous l’onglet Configurer pour votre espace de noms Event Hubs.

Pour mettre à jour le paramètre de diagnostic afin d’inclure la diffusion en continu, vous devez disposer de l’autorisation ListKey sur la règle d’autorisation Event Hubs. Il n’est pas nécessaire que l’espace de noms Event Hubs se trouve dans le même abonnement que l’abonnement qui génère des journaux d’activité, à condition que l’utilisateur configurant le paramètre dispose d’un accès RBAC aux deux abonnements, et que ces derniers se trouvent dans le même locataire AAD.

## <a name="create-a-diagnostic-setting"></a>Créer un paramètre de diagnostic
Envoyer des journaux de plateforme au hub d’événements et à d’autres destinations en créant un paramètre de diagnostic pour une ressource Azure. Pour plus d’informations, consultez [Créer un paramètre de diagnostic pour collecter des journaux et des métriques dans Azure](diagnostic-settings.md).

## <a name="collect-data-from-compute-resources"></a>Recueillir des données à partir de ressources de calcul
Les paramètres de diagnostic collectent les journaux des ressources pour les ressources de calcul Azure comme n’importe quelle autre ressource, mais pas leur système d’exploitation invité ou leurs charges de travail. Pour collecter ces données, installez [l’agent Log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Utilisation des données de journal de hubs d’événements
Les journaux d’activité de plateformes à partir de hubs d’événements sont enregistrés au format JSON avec les éléments du tableau suivant.

| Nom de l’élément | Description |
| --- | --- |
| enregistrements |Un tableau regroupant tous les événements de journal de cette charge utile. |
| time |L’heure à laquelle l’événement s’est produit. |
| catégorie |La catégorie de journal associée à cet événement. |
| resourceId |L’ID de la ressource qui a généré cet événement. |
| operationName |Nom de l’opération. |
| level |facultatif. Indique le niveau de l’événement de journal. |
| properties |Les propriétés de l’événement. Celles-ci varient pour chaque service Azure, comme décrit dans [](). |


Voici des exemples de données de sortie provenant d’Event Hubs pour un journal de ressource :

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Étapes suivantes

* [Découvrez plus d’informations sur les journaux de ressource](platform-logs-overview.md)
* [Créer un paramètre de diagnostic pour collecter les journaux et les mesures dans Azure](diagnostic-settings.md).
* [Diffuser en continu des journaux d’activité Azure Active Directory avec Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Prise en main d’Azure Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

