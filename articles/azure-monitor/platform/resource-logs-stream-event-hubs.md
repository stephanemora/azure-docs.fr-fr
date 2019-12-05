---
title: Diffuser en continu les journaux Azure Monitor vers un Event Hub
description: Découvrez comment envoyer en streaming des journaux de ressources à des hubs d’événements pour envoyer des données à des systèmes externes tels que des solutions SIEM tierces et d’autres solutions d’analytique des journaux.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 680570c5102f656b2b2d2e05f9e08f51fe892f44
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304948"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Diffuser en continu les journaux d’activité de ressources Azure vers Azure Event Hubs
Les [journaux de ressources](resource-logs-overview.md) dans Azure fournissent des informations complètes et fréquentes sur le fonctionnement interne d’une ressource Azure. Cet article décrit la diffusion en continu des journaux d’activité de ressources à des hubs d’événements pour envoyer des données à des systèmes externes tels que des solutions SIEM tierces et d'autres solutions d'analytique des journaux d'activité.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>Comment utiliser les journaux d’activité de ressources envoyés à un Event Hub
Diffusez les journaux d’activité de ressources dans Azure vers Event hubs pour fournir les fonctionnalités suivantes :

* **Diffuser en continu les journaux d’activité vers des systèmes de journalisation et de télémétrie tiers** : diffusez tous vos journaux d’activité des ressources vers un hub d’événements unique pour envoyer les données de journal d’activité vers un outil tiers SIEM ou Log Analytics.
* **Créer une plateforme de journalisation et de télémétrie personnalisée** : la nature hautement évolutive de l’abonnement aux event hubs vous permet d’ingérer de manière flexible les journaux d’activité de ressources dans une plateforme de télémétrie personnalisée. Pour plus d’informations, consultez [Conception et dimensionnement d’une plateforme de télémétrie à échelle mondiale sur Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

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
Si vous n’en avez pas encore, vous devez [créer un hub d’événements](../../event-hubs/event-hubs-create.md). Si vous avez précédemment diffusé en continu des journaux d’activité de ressources vers cet espace de noms Event Hubs, cet Event Hub sera réutilisé.

La stratégie d’accès partagé pour l’espace de noms définit les autorisations dont dispose le mécanisme de diffusion en continu. La diffusion en continu vers Event Hubs requiert des autorisations de gestion, d’envoi et d’écoute. Vous pouvez créer ou modifier les stratégies d’accès partagé dans le Portail Microsoft Azure sous l’onglet Configurer pour votre espace de noms Event Hubs.

Pour mettre à jour le paramètre de diagnostic afin d’inclure la diffusion en continu, vous devez disposer de l’autorisation ListKey sur la règle d’autorisation Event Hubs. Il n’est pas nécessaire que l’espace de noms Event Hubs se trouve dans le même abonnement que l’abonnement qui génère des journaux d’activité, à condition que l’utilisateur configurant le paramètre dispose d’un accès RBAC aux deux abonnements, et que ces derniers se trouvent dans le même locataire AAD.

## <a name="create-a-diagnostic-setting"></a>Créer un paramètre de diagnostic
Les journaux d’activité de ressources ne sont pas collectées par défaut. Envoyez-les à un hub d’événements et à d’autres destinations en créant un paramètres de diagnostic pour une ressource Azure. Pour plus d’informations, consultez [Créer un paramètre de diagnostic pour collecter des journaux et des métriques dans Azure](diagnostic-settings.md).

## <a name="stream-data-from-compute-resources"></a>Diffusion de données à partir des ressources de calcul
Le processus décrit dans cet article concerne les ressources autres que les ressources de calcul, comme décrit dans [Vue d’ensemble des journaux d’activité de ressources Azure](diagnostic-settings.md).
Diffusez en continu des journaux d’activité de ressources à partir des ressources de calcul Azure à l’aide de l’agent Diagnostics Azure pour Windows. Pour plus de détails, consultez [Diffusion des données de Diagnostics Azure dans le chemin réactif à l’aide d’Event Hubs](diagnostics-extension-stream-event-hubs.md).


## <a name="consuming-log-data-from-event-hubs"></a>Utilisation des données de journal de hubs d’événements
Lorsque vous utilisez des journaux d’activité de ressources à partir d’Event hubs, ceux-ci sont enregistrés au format JSON avec les éléments du tableau suivant.

| Nom de l’élément | Description |
| --- | --- |
| records |Un tableau regroupant tous les événements de journal de cette charge utile. |
| time |L’heure à laquelle l’événement s’est produit. |
| category |La catégorie de journal associée à cet événement. |
| resourceId |L’ID de la ressource qui a généré cet événement. |
| operationName |Nom de l’opération. |
| level |facultatif. Indique le niveau de l’événement de journal. |
| properties |Les propriétés de l’événement. Celles-ci varient pour chaque service Azure, comme décrit dans [](). |


Voici des exemples de données de sortie provenant d’Event Hubs :

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

* [Diffuser en continu des journaux d’activité Azure Active Directory avec Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Découvrir plus d’informations sur les journaux d’activité de ressources Azure](resource-logs-overview.md).
* [Prise en main d’Azure Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

