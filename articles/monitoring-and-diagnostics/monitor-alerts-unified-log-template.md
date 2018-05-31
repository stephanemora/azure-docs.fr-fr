---
title: Créer une alerte de journal avec un modèle Resource Manager | Microsoft Docs
description: Découvrez comment créer une alerte de journal à l’aide d’un modèle Azure Resource Manager et d’une API.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 14a2560d91fd0f8dcc729b32c7155c4b74aa8aa1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204156"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Créer une alerte de journal avec un modèle Resource Manager
Cet article explique comment gérer des [alertes de journal](monitor-alerts-unified-log.md) par programmation à grande l’échelle, dans Azure à l’aide d’un [modèle Azure Resource Manager](..//azure-resource-manager/resource-group-authoring-templates.md) par le biais d’[Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) et [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md). Actuellement, Azure Alerts prend en charge les alertes de journal sur des requêtes provenant d’[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) et d’[Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Gestion des alertes de journal sur Log Analytics
Les alertes de journal pour [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) sont intégrées dans la [nouvelle expérience d’alertes Azure](monitoring-overview-unified-alerts.md). Elles font toujours appel aux API Log Analytics et restent compatibles avec le schéma utilisé précédemment pour gérer les [alertes dans le portail OMS](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> À compter du 14 mai 2018, toutes les alertes d’un espace de travail seront automatiquement étendues à Azure. Un utilisateur peut volontairement étendre des alertes à Azure avant le 14 mai 2018. Pour plus d’informations, consultez [Étendre (copier) des alertes du portail OMS dans Azure](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Les alertes de journal pour Log Analytics sont créées par le biais de règles d’alerte exécutant une recherche enregistrée à intervalles réguliers. Si les résultats de la requête correspondent aux critères spécifiés, un enregistrement d’alerte est créé et une ou plusieurs actions sont exécutées. 

Les modèles de ressources pour la [recherche Log Analytics enregistrée](../log-analytics/log-analytics-log-searches.md) et les [alertes Log Analytics](../log-analytics/log-analytics-alerts.md) sont disponibles dans la section Log Analytics de la documentation. Pour en savoir plus, consultez l’article [Ajout de recherches et d’alertes enregistrées Log Analytics ](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md), qui contient des exemples d’illustration et des détails sur le schéma.

### <a name="using-resource-template-via-apipowershell"></a>Utilisation du modèle de ressource par le biais d’une API/de Powershell
L’API REST d’alerte Log Analytics est un service RESTful qui est accessible par le biais de l’API REST Azure Resource Manager. L’API est ainsi accessible à partir d’une ligne de commande PowerShell et génère pour vous des résultats de recherche au format JSON, ce qui vous permet d’utiliser ces résultats, par programmation, de différentes manières.

Pour en savoir plus, consultez l’article [Créer et gérer des règles d’alerte dans Log Analytics avec l’API REST](../log-analytics/log-analytics-api-alerts.md), qui contient notamment des exemples d’accès à l’API à partir de Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Gestion des alertes de journal sur Application Insights
Les alertes de journal pour Azure Application Insights ont été introduites dans le cadre des nouvelles alertes Azure dans Azure Monitor. Par conséquent, elles s’exécutent sous l’API Azure Monitor en tant que groupe d’opérations REST de [règles de requêtes planifiées](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

### <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Les alertes de journal pour les ressources Application Insights ont le type `Microsoft.Insights/scheduledQueryRules/`. Pour plus d’informations sur ce type de ressource, consultez [Azure Monitor - Référence de l’API des règles de requêtes planifiées](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Voici la structure pour un modèle de ressource basé sur la [création de règles de requêtes planifiées](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate), avec un exemple de jeu de données en tant que variables.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Le champ Tag avec hidden-link vers la ressource cible est obligatoire en cas d’utilisation d’appel d’API de [règles de requêtes planifiées](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) ou de modèle de ressource. 

L’exemple json ci-dessus peut par exemple être enregistré en tant que sampleScheduledQueryRule.json pour les besoins de cette procédure pas à pas, et peut être déployé à l’aide d’[Azure Resource Manager dans le portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Utilisation du modèle de ressource par le biais de l’interface CLI/Powershell
L’API Azure Monitor - Règles de requêtes planifiées est une API REST entièrement compatible avec l’API REST Azure Resource Manager. Elle peut donc être utilisée par le biais de Powershell à l’aide d’applets de commande Resource Manager en plus d’Azure CLI.

Vous trouverez ci-dessous une illustration de l’utilisation par le biais de l’applet de commande Azure Resource Manager PowerShell pour l’exemple de modèle de ressource présenté plus haut (sampleScheduledQueryRule.json) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Vous trouverez ci-dessous une illustration de l’utilisation par le biais de commandes Azure Resource Manager dans Azure CLI pour l’exemple de modèle de ressource présenté plus haut (sampleScheduledQueryRule.json) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Une fois l’opération réussie, le code 201 est retourné pour signaler la création de la nouvelle règle d’alerte, ou l’état 200 si une règle d’alerte existante a été modifiée.


## <a name="next-steps"></a>Étapes suivantes
* Comprendre les [actions Webhook pour les alertes de journal](monitor-alerts-unified-log-webhook.md)
* En savoir plus sur les nouvelles [Alertes Azure](monitoring-overview-unified-alerts.md)
* En savoir plus sur [Application Insights](../application-insights/app-insights-analytics.md)
* En savoir plus sur [Log Analytics](../log-analytics/log-analytics-overview.md).   
