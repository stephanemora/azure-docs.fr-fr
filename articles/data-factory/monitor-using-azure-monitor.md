---
title: Surveiller les fabriques de données à l’aide d’Azure Monitor
description: Apprenez à utiliser Azure Monitor pour surveiller les pipelines Azure Monitor en activant les journaux de diagnostic à partir des informations de Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 8325b4ef6b89a76eeec418386cec4922cb5916b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979148"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Déclencher des alertes et surveiller les fabriques de données avec Azure Monitor

Les applications cloud sont complexes, et comprennent de nombreux éléments mobiles. Les analyses fournissent des données pour vous aider à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Les analyses vous aident également à éviter des problèmes potentiels et à résoudre les problèmes précédents.

Vous pouvez utiliser les données de surveillance pour obtenir des informations détaillées sur votre application. Cette connaissance vous aide à améliorer les performances et la maintenabilité de l’application. Elle vous aide également à automatiser des actions qui nécessitent autrement une intervention manuelle.

Azure Monitor fournit des métriques et des journaux d’activité de niveau de base d’infrastructure pour la plupart des services Azure. Les journaux de diagnostic Azure sont générés par une ressource et fournissent des informations complètes et fréquentes sur le fonctionnement de cette ressource. Et Azure Data Factory écrit des journaux de diagnostic dans le moniteur.

Pour plus de détails, voir [Vue d’ensemble d’Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Conservation des données d’Azure Data Factory

Data Factory ne stocke les données d’exécution du pipeline que pendant 45 jours. Utilisez Monitor si vous souhaitez conserver ces données pendant une période plus longue. Monitor vous permet d’acheminer les journaux de diagnostic à des fins d’analyse. Vous pouvez également les conserver dans un compte de stockage pour disposer d’informations sur la fabrique pendant la durée choisie.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

* Enregistrez vos journaux de diagnostic dans un compte de stockage à des fins d’audit ou d’inspection manuelle. Vous pouvez utiliser les paramètres de diagnostic pour spécifier la durée de rétention en jours.
* Diffusez en continu les journaux vers Azure Event Hubs. Les journaux deviennent des entrées pour un service partenaire ou une solution d’analyse personnalisée telle que Power BI.
* Analysez les journaux avec Log Analytics.

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hub qui ne se trouve pas dans le même abonnement que la ressource émettrice des journaux. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC (contrôle d’accès en fonction du rôle) approprié aux deux abonnements.

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic

### <a name="diagnostic-settings"></a>Paramètres de diagnostic

Utilisez des paramètres de diagnostic pour configurer les journaux de diagnostic des ressources autres que de calcul. Les paramètres d’un contrôle de ressource présentent les caractéristiques suivantes :

* Ils spécifient l’emplacement d’envoi des journaux de diagnostic. Il peut s’agir, part exemple, d’un compte de stockage Azure, d’un Event Hub Azure ou de journaux Azure Monitor.
* Ils spécifient les catégories de journal qui sont envoyées.
* Ils spécifient la durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage.
* Une durée de rétention de zéro jour signifie que les journaux d’activité sont conservés indéfiniment. Autrement, la valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2 147 483 647.
* Si des stratégies de rétention sont définies, mais que le stockage des journaux d’activité dans un compte de stockage est désactivé, les stratégies de rétention n’ont aucun effet. Par exemple, cette condition peut se produire lorsque seules les options Event Hubs ou journaux Azure Monitor sont sélectionnées.
* Les stratégies de rétention sont appliquées par jour. La séparation entre les jours se produit à minuit en temps universel coordonné (UTC). À la fin de la journée, les journaux des jours qui dépassent la stratégie de rétention sont supprimés. Par exemple, si vous avez une stratégie de rétention d’une journée, au début de la journée les journaux antérieurs à la veille sont supprimés.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Activer les journaux de diagnostic via l’API REST Azure Monitor

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Créer ou mettre à jour un paramètre de diagnostic dans l’API REST Azure Monitor

##### <a name="request"></a>Requête

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* Remplacez `{api-version}` par `2016-09-01`.
* Remplacez `{resource-id}` par l’ID de la ressource dont vous voulez modifier les paramètres de diagnostic. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Attribuez à l’en-tête `Content-Type` la valeur `application/json`.
* Définissez l’en-tête d’autorisation sur le jeton web JSON que vous avez obtenu d’Azure Active Directory (AAD). Pour plus d’informations, consultez [Authentification des demandes](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| **storageAccountId** |String | ID de ressource du compte de stockage auquel vous voulez envoyer les journaux de diagnostic. |
| **serviceBusRuleId** |String | ID de règle Service Bus de l’espace de noms Service Bus dans lequel vous voulez que les Event Hubs soient créés pour la diffusion en continu des journaux de diagnostic. L’ID de règle a le format `{service bus resource ID}/authorizationrules/{key name}`.|
| **workspaceId** | Type complexe | Tableau de fragments de temps de métrique et leurs stratégies de rétention. La valeur de cette propriété est vide. |
|**metrics**| Valeurs de paramètre de l’exécution de pipeline à passer au pipeline appelé| Objet JSON qui mappe des noms de paramètres à des valeurs d’arguments. |
| **logs**| Type complexe| Nom d’une catégorie de journal de diagnostic pour un type de ressource. Pour obtenir la liste des catégories de journal de diagnostic pour une ressource, effectuez une opération d’obtention (GET) des paramètres de diagnostic. |
| **category**| String| Tableau de catégories de journal et leurs stratégies de rétention. |
| **timeGrain** | String | Granularité des métriques capturées au format de durée ISO 8601. La valeur de la propriété doit être `PT1M`, qui spécifie une minute. |
| **activé**| Boolean | Indique si la collection de la métrique ou catégorie de journal est activée pour cette ressource. |
| **retentionPolicy**| Type complexe| Décrit la stratégie de rétention pour une métrique ou une catégorie de journal. Cette propriété est utilisée uniquement pour les comptes de stockage. |
|**days**| Int| Nombre de jours durant lesquels les métriques ou journaux sont conservés. Si la valeur de la propriété est 0, les journaux sont conservés indéfiniment. Cette propriété est utilisée uniquement pour les comptes de stockage. |

##### <a name="response"></a>response

200 OK.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Obtenez des informations sur le paramètre de diagnostic dans l’API REST Azure Monitor

##### <a name="request"></a>Requête

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* Remplacez `{api-version}` par `2016-09-01`.
* Remplacez `{resource-id}` par l’ID de la ressource dont vous voulez modifier les paramètres de diagnostic. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Attribuez à l’en-tête `Content-Type` la valeur `application/json`.
* Définissez l’en-tête d’autorisation sur un jeton web JSON que vous avez obtenu d’Azure Active Directory. Pour plus d’informations, consultez [Authentification des demandes](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>response

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
Pour plus d’informations, voir [Paramètres de diagnostic](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schéma des journaux et des événements

### <a name="monitor-schema"></a>Schéma Azure Monitor

#### <a name="activity-run-log-attributes"></a>Attributs du journal d’exécution d’activité

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriété | Type | Description | Exemple |
| --- | --- | --- | --- |
| **Niveau** |String | Niveau des journaux de diagnostic. Pour les journaux d’exécution d’activité, affectez la valeur 4 à la propriété. | `4` |
| **correlationId** |String | ID unique pour le suivi d’une demande particulière. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Heure de l’événement au format UTC d’intervalle de temps `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| ID de l’exécution d’activité. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| ID de l’exécution de pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | ID associé à la ressource de fabrique de données. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Catégorie des journaux de diagnostic. Affectez la valeur `ActivityRuns` à la propriété. | `ActivityRuns` |
|**level**| String | Niveau des journaux de diagnostic. Affectez la valeur `Informational` à la propriété. | `Informational` |
|**operationName**| String | Nom de l’activité avec cet état. Si l’activité est la pulsation de début, la valeur de la propriété est `MyActivity -`. Si l’activité est la pulsation de fin, la valeur de la propriété est `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**pipelineName**| String | Nom du pipeline. | `MyPipeline` |
|**activityName**| String | Nom de l’activité. | `MyActivity` |
|**start**| String | Heure de début des exécutions d’activité au format UTC d’intervalle de temps. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | Heure de fin des exécutions d’activité au format UTC d’intervalle de temps. Si le journal de diagnostic indique qu’une activité a démarré mais qu’elle n’est pas encore terminée, la valeur de la propriété est `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Attributs du journal d’exécution de pipeline

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriété | Type | Description | Exemple |
| --- | --- | --- | --- |
| **Niveau** |String | Niveau des journaux de diagnostic. Pour les journaux d’exécution d’activité, affectez la valeur 4 à la propriété. | `4` |
| **correlationId** |String | ID unique pour le suivi d’une demande particulière. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Heure de l’événement au format UTC d’intervalle de temps `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| ID de l’exécution de pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | ID associé à la ressource de fabrique de données. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Catégorie des journaux de diagnostic. Affectez la valeur `PipelineRuns` à la propriété. | `PipelineRuns` |
|**level**| String | Niveau des journaux de diagnostic. Affectez la valeur `Informational` à la propriété. | `Informational` |
|**operationName**| String | Nom du pipeline, ainsi que son état. Une fois l’exécution du pipeline terminée, la valeur de la propriété est `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | Nom du pipeline. | `MyPipeline` |
|**start**| String | Heure de début des exécutions d’activité au format UTC d’intervalle de temps. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | Heure de fin des exécutions d’activité au format UTC d’intervalle de temps. Si le journal de diagnostic indique qu’une activité a démarré mais qu’elle n’est pas encore terminée, la valeur de la propriété est `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**statut**| String | État final de l’exécution de pipeline. Les valeurs possibles de la propriété sont `Succeeded` et `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Attributs du journal d’exécution de déclencheur

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Propriété | Type | Description | Exemple |
| --- | --- | --- | --- |
| **Niveau** |String | Niveau des journaux de diagnostic. Pour les journaux d’exécution d’activité, affectez la valeur 4 à la propriété. | `4` |
| **correlationId** |String | ID unique pour le suivi d’une demande particulière. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Heure de l’événement au format UTC d’intervalle de temps `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| ID de l’exécution du déclencheur. | `08587023010602533858661257311` |
|**resourceId**| String | ID associé à la ressource de fabrique de données. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Catégorie des journaux de diagnostic. Affectez la valeur `PipelineRuns` à la propriété. | `PipelineRuns` |
|**level**| String | Niveau des journaux de diagnostic. Affectez la valeur `Informational` à la propriété. | `Informational` |
|**operationName**| String | Nom du déclencheur avec son état final, qui indique si le déclencheur a fonctionné correctement. Si la pulsation a réussi, la valeur de la propriété est `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| String | Nom du déclencheur. | `MyTrigger` |
|**triggerType**| String | Type du déclencheur. Les valeurs possibles de la propriété sont `Manual Trigger` et `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| String | Événement du déclencheur. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Heure de début de l’action du défenseur au format UTC d’intervalle de temps. | `2017-06-26T20:55:29.5007959Z`|
|**statut**| String | État final indiquant si le déclencheur a fonctionné correctement. Les valeurs possibles de la propriété sont `Succeeded` et `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Schéma Log Analytics

Log Analytics hérite du schéma d’Azure Monitor avec les exceptions suivantes :

* La première lettre de chaque nom de colonne est majuscule. Par exemple, le nom de colonne « correlationId » dans Monitor est « CorrelationId » dans Log Analytics.
* Il n’y a pas de colonne « Level ».
* La colonne des « properties » des propriétés dynamiques est conservée en tant que type d’objet blob JSON dynamique.

    | Colonne Azure Monitor | Colonne Log Analytics | Type |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | Dynamique |
    | $.properties.Annotations | Annotations | Dynamique |
    | $.properties.Input | Entrée | Dynamique |
    | $.properties.Output | Output | Dynamique |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | Error | Dynamique |
    | $.properties.Predecessors | Predecessors | Dynamique |
    | $.properties.Parameters | Paramètres | Dynamique |
    | $.properties.SystemParameters | SystemParameters | Dynamique |
    | $.properties.Tags | Balises | Dynamique |
    
## <a name="metrics"></a>Mesures

Monitor vous apporte une visibilité sur les performances et l’intégrité de vos charges de travail Azure. Le type de données d’analyse le plus important de Monitor est la métrique, également appelée compteur de performances. Des métriques sont émises par la plupart des ressources Azure. Monitor propose plusieurs façons de configurer et d’utiliser ces métriques à des fins d’analyse et de résolution des problèmes.

Azure Data Factory version 2 émet les métriques suivantes.

| **Mesure**           | **Nom d’affichage de la métrique**         | **Unité** | **Type d’agrégation** | **Description**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Métriques d’exécutions de pipeline ayant abouti | Count    | Total                | Nombre total d’exécutions de pipeline ayant réussi dans une fenêtre d’une minute. |
| PipelineFailedRuns   | Métriques d’exécutions de pipeline ayant échoué    | Count    | Total                | Nombre total d’exécutions de pipeline ayant échoué dans une fenêtre d’une minute.    |
| ActivitySucceededRuns | Métriques d’exécutions d’activité ayant abouti | Count    | Total                | Nombre total d’exécutions d’activité ayant réussi dans une fenêtre d’une minute.  |
| ActivityFailedRuns   | Métriques d’exécutions d’activité ayant échoué    | Count    | Total                | Nombre total d’exécutions d’activité ayant échoué dans une fenêtre d’une minute.     |
| TriggerSucceededRuns | Métriques d’exécutions de déclencheur ayant abouti  | Count    | Total                | Nombre total d’exécutions de déclencheur ayant réussi dans une fenêtre d’une minute.   |
| TriggerFailedRuns    | Métriques d’exécutions de déclencheur ayant échoué     | Count    | Total                | Nombre total d’exécutions de déclencheur ayant échoué dans une fenêtre d’une minute.      |

Pour accéder aux métriques, suivez les instructions fournies dans [Plateforme de données Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Seuls les événements d’exécution de pipeline et d’activité déclenchés et finis sont émis. Les exécutions en cours et de bac à sable (sandbox)/débogage **ne sont pas** émises. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Superviser les métriques de Data Factory avec Azure Monitor

Vous pouvez utiliser l’intégration de Data Factory avec Monitor pour acheminer des données vers Monitor. Cette intégration est utile dans les scénarios suivants :

* Vous souhaitez écrire des requêtes complexes sur un ensemble varié de mesures publiées par la Data Factory sur Monitor. Vous pouvez créer des alertes personnalisées sur ces requêtes via Monitor.

* Vous souhaitez surveiller l’activité de toutes les fabriques de données. Vous pouvez acheminer les données de plusieurs fabriques de données vers un seul espace de travail Monitor.

Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configurer les paramètres de diagnostic et l’espace de travail

Créez ou ajoutez des paramètres de diagnostic pour votre fabrique de données.

1. Dans le portail, accédez à Monitor. Sélectionnez **Paramètres** > **Paramètres de diagnostic**.

1. Sélectionnez la fabrique de données pour laquelle vous souhaitez définir un paramètre de diagnostic.

1. S’il n’existe aucun paramètre sur la fabrique de données sélectionnée, vous êtes invité à en créer un. Sélectionnez **Activer les diagnostics**.

   ![Créer un paramètre de diagnostic s’il n’en existe pas](media/data-factory-monitor-oms/monitor-oms-image1.png)

   S’il existe des paramètres sur la fabrique de données, vous voyez une liste de paramètres déjà configurés sur cette fabrique de données. Sélectionnez **Ajouter le paramètre de diagnostic**.

   ![Ajouter un paramètre de diagnostic s’il existe des paramètres](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Nommez votre paramètre, sélectionnez **Envoyer à log Analytics**, puis sélectionnez un espace de travail dans **Espace de travail Log Analytics**.

    ![Nommer vos paramètres et sélectionner un espace de travail Log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Sélectionnez **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres pour cette fabrique de données. Les journaux de diagnostic sont diffusés en continu vers cet espace de travail dès que de nouvelles données d’événement sont générées. Jusqu’à 15 minutes peuvent s’écouler entre l’émission d’un événement et son affichage dans Log Analytics.

* En mode _Spécifique de la ressource_, les journaux de diagnostic d’Azure Data Factory circulent dans les tables _ADFPipelineRun_, _ADFTriggerRun_ et _ADFActivityRun_
* En mode _Diagnostics Azure_, les journaux de diagnostic circulent dans la table _AzureDiagnostics_

> [!NOTE]
> Étant donné qu’une table de journal Azure ne peut pas comporter plus de 500 colonnes, nous vous recommandons vivement de sélectionner le mode spécifique de la ressource. Pour plus d’informations, consultez [Limitations connues de Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installer Azure Data Factory Analytics à partir de la Place de marché Azure

![Accédez à la « Place de marché Azure », entrez « Filtre Analytics », puis sélectionnez « Azure Data Factory Analytics (préversion) »](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Détails sur « Azure Data Factory Analytics (préversion) »](media/data-factory-monitor-oms/monitor-oms-image4.png)

Cliquez sur**Créer**, puis sélectionnez **Espace de travail OMS** et **Paramètres de l’espace de travail OMS**.

![Création d’une solution](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Surveiller les métriques de Data Factory

L’installation d’Azure Data Factory Analytics crée un ensemble de vues par défaut qui activent les métriques suivantes :

- Exécutions d’ADF - 1) Exécutions de pipeline par Data Factory
 
- Exécution d’ADF- 2) Exécution d’activité par fabrique de données

- Exécutions d’ADF- 3) Exécutions de déclencheur par Data Factory

- Erreurs d’ADF - 1) 10 premières erreurs de pipeline par Data Factory

- Erreurs d’ADF - 2) 10 premières exécution d'activité par Data Factory

- Erreur d’ADF - 3) 10 premières erreurs de déclencheur par Data Factory

- Statistiques d’ADF - 1) Exécutions d’activité par type

- Statistiques d’ADF - 2) Exécutions de déclencheur par type

- Statistiques d’ADF - 3) Durée maximale d’exécutions du pipeline

![Fenêtre avec « Classeurs (préversion) » et « AzureDataFactoryAnalytics » en surbrillance](media/data-factory-monitor-oms/monitor-oms-image6.png)

Vous pouvez visualiser les métriques ci-dessus, consulter les requêtes derrière ces métriques, modifier les requêtes, créer des alertes, et exécuter d’autres action.

![Représentation graphique des exécutions de pipeline par Data Factory](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (préversion) envoie des journaux de diagnostic à des tables de destination _Spécifique de la ressource_. Vous pouvez écrire des requêtes portant sur les tables suivantes : _ADFPipelineRun_, _ADFTriggerRun_ et _ADFActivityRun_.

## <a name="alerts"></a>Alertes

Connectez-vous au portail Azure, puis sélectionnez **Monitor** > **Alertes** pour créer des alertes.

![Alertes dans le menu du portail](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Créer des alertes

1. Sélectionnez **+ Nouvelle règle d’alerte** pour créer une nouvelle alerte.

    ![Nouvelle règle d’alerte](media/monitor-using-azure-monitor/alerts_image4.png)

1. Définissez la condition de l’alerte.

    > [!NOTE]
    > Veillez à sélectionner **Toutes** dans la liste déroulante **Filtrer par type de ressource**.

    ![« Définir la condition d’alerte » > « Sélectionner la cible », pour ouvrir le volet « Sélectionner une ressource » ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![« Définir la condition d’alerte » > « Ajouter des critères », pour ouvrir le volet « Configurer la logique du signal »](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Volet « Configurer la logique du signal »](media/monitor-using-azure-monitor/alerts_image7.png)

1. Définissez les détails de l’alerte.

    ![Détails de l’alerte](media/monitor-using-azure-monitor/alerts_image8.png)

1. Définissez le groupe d’actions.

    ![Créer une règle, avec « Nouveau groupe d’actions » en surbrillance](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Créer un nouveau groupe d’action](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configurer le courrier, les SMS, les notifications push et la voix](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Définir un groupe d’actions](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Étapes suivantes
[Surveiller et gérer les pipelines par programmation](monitor-programmatically.md)
