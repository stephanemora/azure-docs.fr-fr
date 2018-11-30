---
title: Création d’alertes de métrique de journaux dans Azure Monitor
description: Didacticiel sur la création d’alertes de métrique en temps quasi réel sur les données Log Analytics courantes.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 92474bdead021429792f5d51a28ffb7bafc5be2b
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334242"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Créer des alertes de métrique de journaux dans Azure Monitor  

## <a name="overview"></a>Vue d’ensemble
Azure Monitor prend en charge le [type d’alerte de métrique](monitoring-near-real-time-metric-alerts.md), qui présente certains avantages par rapport aux [alertes classiques](alert-metric-classic.md). Des métriques sont disponibles pour une [longue liste de services Azure](monitoring-supported-metrics.md). Cet article explique comment utiliser un sous-ensemble pour une ressource - `Microsoft.OperationalInsights/workspaces`. 

Vous pouvez utiliser des alertes de métrique sur des journaux Log Analytics courants, extraits en tant que métriques dans le cadre des métriques à partir de journaux, notamment des ressources dans Azure ou en local. Les solutions Log Analytics prises en charge sont répertoriées ci-dessous :
- [Les compteurs de performance](../azure-monitor/platform/data-sources-performance-counters.md) pour les machines Windows et Linux
- [Enregistrements de pulsations pour Agent Health](../azure-monitor/insights/solution-agenthealth.md)
- Enregistrements de la [gestion des mises à jour](../automation/automation-update-management.md)
- Journaux sur les [données d’événement](../azure-monitor/platform/data-sources-windows-events.md)
 
L’utilisation **d’alertes de métrique de journaux** présente de nombreux avantages par rapport à celle [d’alertes de journal](alert-log.md) basées sur une requête. Certains de ces avantages sont indiqués ci-dessous :
- Les alertes de métrique proposent une fonctionnalité de supervision en tant quasi réel et les alertes de métrique pour des journaux dupliquent les données de la source du journal pour garantir la même chose.
- Les alertes de métrique sont de type avec état, elles envoient des notifications uniquement quand l’alerte est déclenchée et quand elle est résolue. À l’inverse, les alertes de journal, qui sont sans état, se déclenchent à chaque intervalle si la condition d’alerte est remplie.
- Les alertes de métrique de journal fournissent plusieurs dimensions, ce qui permet d’appliquer des filtres sur des valeurs spécifiques telles que les ordinateurs, le type de système d’exploitation, etc. de façon plus simple, sans avoir besoin d’écrire des requêtes dans les analyses.

> [!NOTE]
> La métrique et/ou la dimension spécifique ne s’affichera que si des données correspondantes existent pour la période choisie. Ces métriques sont disponibles pour les clients qui ont des espaces de travail Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Métriques et dimensions prises en charge pour les journaux
 Les alertes de métrique prennent en charge la génération d’alertes pour les métriques qui utilisent des dimensions. Vous pouvez utiliser les dimensions pour filtrer votre métrique au niveau approprié. La liste complète des métriques prises en charge pour les journaux des [espaces de travail Log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces) est répertoriée, pour toutes les solutions prises en charge.

> [!NOTE]
> Pour afficher les métriques prises en charge pour l’extraction de l’espace de travail Log Analytics via [Azure Monitor - Métriques](monitoring-metric-charts.md), une alerte de métrique de journal doit être créée pour la métrique concernée. Les dimensions choisies dans l’alerte de métrique pour des journaux apparaissent uniquement pour l’exploration via Azure Monitor - Métriques.

# <a name="creating-metric-alert-for-log-analytics"></a>Création d’alerte de métrique pour Log Analytics
Les données de métrique des journaux courants sont acheminées avant leur traitement dans Log Analytics, dans Azure Monitor - Métriques. Cela permet aux utilisateurs d’exploiter les fonctionnalités de la plateforme Métrique, ainsi que les alertes de métrique, et notamment de profiter de la génération d’alertes à une fréquence de 1 minute. Vous trouverez ci-dessous les méthodes de création d’une alerte de métrique pour des journaux.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Conditions préalables pour la création d’une alerte de métrique pour des journaux
Avant que la métrique pour des journaux rassemblés sur des données Log Analytics fonctionne, les éléments suivants doivent être configurés et disponibles :
1. **Espace de travail Log Analytics actif** : un espace de travail Log Analytics actif doit être présent. Pour plus d’informations, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../log-analytics/log-analytics-quick-create-workspace.md).
2. **Agent configuré pour l’espace de travail Log Analytics** : un agent doit être configuré pour les machines virtuelles Azure ou les machines virtuelles locales, afin d’envoyer des données à l’espace de travail Log Analytics utilisé dans l’étape précédente. Pour plus d’informations, consultez [Présentation des agents Azure pour surveiller les machines virtuelles Azure](../azure-monitor/platform/agents-overview.md).
3. **Solutions Log Analytics prises en charge installées** : une solution Log Analytics doit être configurée et doit envoyer des données à l’espace de travail Log Analytics ; les solutions prises en charge sont les [compteurs de performances pour Windows et Linux](../azure-monitor/platform/data-sources-performance-counters.md), les [enregistrements de pulsation pour Agent Health](../azure-monitor/insights/solution-agenthealth.md), la [gestion des mises à jour] et les [données d’événement](../azure-monitor/platform/data-sources-windows-events.md).
4. **Solutions Log Analytics configurées pour envoyer des journaux** : une solution Log Analytics doit avoir les journaux/données correspondant aux [métriques prises en charge pour les espaces de travail Log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces) activés. Par exemple, le compteur *% Available Memory* (% de mémoire disponible) doit d’abord être configuré dans la solution [Compteurs de performances](../azure-monitor/platform/data-sources-performance-counters.md).

## <a name="configuring-metric-alert-for-logs"></a>Configuration d’une alerte de métrique pour des journaux
 Des alertes de métrique peuvent être créées et gérées à l’aide du Portail Azure, des modèles Resource Manager, de l’API REST, de PowerShell et d’Azure CLI. Étant donné que les alertes de métrique pour des journaux sont une variante des alertes de métrique, une fois que les conditions préalables sont remplies, une alerte de métrique pour des journaux peut être créée pour l’espace de travail Log Analytics spécifié. Toutes les caractéristiques et fonctionnalités des [alertes de métrique](monitoring-near-real-time-metric-alerts.md) sont également applicables aux alertes de métrique pour des journaux, notamment le schéma de charge utile, les limites de quota applicables et les prix facturés.

Pour des exemples et des informations détaillées, consultez [Creating and managing metric alerts](https://aka.ms/createmetricalert) (Création et gestion des alertes de métrique). Pour les alertes de métrique pour des journaux, suivez les instructions relatives à la gestion des alertes de métrique et vérifiez les points suivants :
- La cible de l’alerte de métrique est un *espace de travail Log Analytics* valide.
- Le signal choisi pour l’alerte de métrique pour *l’espace de travail Log Analytics* sélectionné est de type **Métrique**.
- Filtrage d’une ressource ou de conditions spécifiques à l’aide de filtres de dimension ; les métriques de journaux sont multidimensionnelles.
- Lors de la configuration de *Signal Logic* (Logique du signal), une alerte unique peut être créée pour s’étendre à plusieurs valeurs de dimension (comme Ordinateur).
- Si vous n’utilisez **pas** le Portail Azure pour créer une alerte de métrique pour *l’espace de travail Log Analytics* sélectionné, l’utilisateur doit commencer par créer manuellement une règle explicite pour convertir les données de journal en métriques à l’aide [d’Azure Monitor - Règles de requête planifiées](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Lors de la création d’une alerte de métrique pour l’espace de travail Log Analytics via le Portail Azure, la règle correspondante pour la conversion de données de journal en métriques via [Azure Monitor - Règles de requête planifiées](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) est automatiquement créée en arrière-plan, *sans qu’une action ou intervention de l’utilisateur ne soit nécessaire*. Pour créer une alerte de métrique pour des journaux autrement qu’avec le Portail Azure, consultez la section [Modèle de ressource pour les alertes de métrique pour des journaux](#resource-template-for-metric-alerts-for-logs) sur les exemples de méthodes de création d’une règle de conversion de journal en métrique basée sur ScheduledQueryRule avant la création d’une alerte de métrique, sinon il n’y aura pas de données pour l’alerte de métrique sur les journaux créée.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Modèle de ressource pour les alertes de métrique pour des journaux
Comme indiqué précédemment, le processus de création des alertes de métrique à partir des journaux est double :
1. Création d’une règle pour l’extraction de métriques de journaux pris en charge à l’aide de l’API scheduledQueryRule
2. Création d’une alerte de métrique pour une métrique extraite d’un journal (dans l’étape 1) et de l’espace de travail Log Analytics en tant que ressource cible

Pour obtenir le même résultat, vous pouvez utiliser l’exemple de modèle Azure Resource Manager ci-dessous, quand la création d’une alerte de métrique dépend de la réussite de la création de la règle d’extraction de métriques à partir de journaux via scheduledQueryRule.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],   
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}

```
Supposons que le code JSON ci-dessus soit enregistré sous le nom metricfromLogsAlert.json, nous pouvons l’associer à un fichier JSON de paramètre pour la création basée sur le modèle de ressource. Vous trouverez ci-dessous un exemple de fichier JSON de paramètre :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan" 
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }    
}
```
En supposant que le fichier de paramètre ci-dessus est enregistré sous le nom metricfromLogsAlert.parameters.json, vous pouvez créer une alerte de métrique pour des journaux à l’aide du [modèle de ressource pour la création dans le Portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md). 

Vous pouvez également utiliser la commande Azure PowerShell ci-dessous :
```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlert.json TemplateParameterFile metricfromLogsAlert.parameters.json
```

Ou déployez le modèle de ressource avec Azure CLI :
```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlert.json --parameters @metricfromLogsAlert.parameters.json
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [alertes de métrique](https://aka.ms/createmetricalert).
* En savoir plus sur les [alertes de journal dans Azure](monitor-alerts-unified-log.md).
* En savoir plus sur les [alertes dans Azure](monitoring-overview-alerts.md).
