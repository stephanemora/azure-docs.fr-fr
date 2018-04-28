---
title: Alertes et recherches enregistrées dans les solutions de gestion | Microsoft Docs
description: Les solutions de gestion incluent généralement des recherches enregistrées dans Log Analytics afin d’analyser les données collectées par la solution.  Elles peuvent également définir des alertes pour avertir l’utilisateur ou appliquer automatiquement une action en réponse à un problème critique.  Cet article décrit comment définir les alertes et recherches enregistrées de Log Analytics dans un modèle Resource Manager, de sorte qu’elles puissent être incluses dans des solutions de gestion.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: bwren, vinagara
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c43e262725bd7b4c4fe5680f514d80112766f991
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2018
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Ajout de recherches et d’alertes enregistrées Log Analytics à une solution de gestion (préversion)

> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.   


Les [solutions de gestion](operations-management-suite-solutions.md) incluent généralement des [recherches enregistrées](../log-analytics/log-analytics-log-searches.md) dans Log Analytics afin d’analyser les données collectées par la solution.  Elles peuvent également définir des [alertes](../log-analytics/log-analytics-alerts.md) pour avertir l’utilisateur ou appliquer automatiquement une action en réponse à un problème critique.  Cet article décrit comment définir les alertes et recherches enregistrées de Log Analytics dans un [modèle Resource Manager](../resource-manager-template-walkthrough.md), de sorte qu’elles puissent être incluses dans des [solutions de gestion](operations-management-suite-solutions-creating.md).

> [!NOTE]
> Les exemples dans cet article utilisent des paramètres et des variables obligatoires ou communs aux solutions de gestion. Ils sont décrits dans la rubrique [Conception et génération d’une solution de gestion dans Azure](operations-management-suite-solutions-creating.md)  

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous êtes déjà familiarisé avec la [création d’une solution de gestion](operations-management-suite-solutions-creating.md) et la structure d’un [modèle Resource Manager](../resource-group-authoring-templates.md) et d’un fichier solution.


## <a name="log-analytics-workspace"></a>Espace de travail Log Analytics
Toutes les ressources dans Log Analytics sont contenues dans un [espace de travail](../log-analytics/log-analytics-manage-access.md).  Comme décrit dans [Espace de travail Log Analytics et compte Automation](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account), l’espace de travail n’est pas inclus dans la solution de gestion mais doit exister avant l’installation de la solution.  Si ce n’est pas le cas, l’installation de la solution échoue.

Le nom de l’espace de travail figure dans le nom de chaque ressource Log Analytics.  Pour cela, utilisez le paramètre **workspace** comme dans l’exemple suivant, qui utilise une ressource SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Version de l’API Log Analytics
Toutes les ressources Log Analytics définies dans un modèle Resource Manager ont une propriété **apiVersion** qui définit la version de l’API que la ressource doit utiliser.  Cette version varie selon que les ressources utilisent [le langage de requête hérité ou mis à niveau](../log-analytics/log-analytics-log-search-upgrade.md).  

 Le tableau suivant indique les versions de l’API Log Analytics pour les recherches enregistrées dans des espaces de travail hérités et mis à niveau : 

| Version de l’espace de travail | Version de l'API | Requête |
|:---|:---|:---|
| v1 (hérité)   | 2015-11-01-preview | Format hérité.<br> Exemple : Type=Event EventLevelName = Error  |
| v2 (mis à niveau) | 2015-11-01-preview | Format hérité.  Converti au format mis à niveau durant l’installation.<br> Exemple : Type=Event EventLevelName = Error<br>Converti au format : Event &#124; where EventLevelName == "Error"  |
| v2 (mis à niveau) | 2017-03-03-preview | Format mis à niveau. <br>Exemple : Event &#124; where EventLevelName == "Error"  |



## <a name="saved-searches"></a>Recherches enregistrées
Ajoutez des [recherches enregistrées](../log-analytics/log-analytics-log-searches.md) à une solution pour permettre aux utilisateurs d’interroger les données collectées par votre solution.  Les recherches enregistrées apparaissent sous **Favoris** dans le portail OMS et sous **Recherches enregistrées** dans le portail Azure.  Une recherche enregistrée est également requise pour chaque alerte.   

Les ressources de [recherche enregistrée Log Analytics](../log-analytics/log-analytics-log-searches.md) ont le type `Microsoft.OperationalInsights/workspaces/savedSearches` et présentent la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }



Chaque propriété d’une recherche enregistrée est décrite dans le tableau suivant. 

| Propriété | Description |
|:--- |:--- |
| category | La catégorie de la recherche enregistrée.  Toutes les recherches enregistrées d’une même solution partagent généralement une catégorie unique. Elles sont ainsi regroupées dans la console. |
| displayname | Le nom à afficher pour la recherche enregistrée dans le portail. |
| query | Requête à exécuter. |

> [!NOTE]
> Vous devrez peut-être utiliser des caractères d’échappement dans la requête si elle inclut des caractères qui peuvent être interprétés comme du code JSON.  Par exemple, si votre recherche était **Type: AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, elle doit être écrite de la façon suivante dans le fichier solution : **Type: AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alertes
Les [alertes Log Analytics](../log-analytics/log-analytics-alerts.md) sont créées par le biais de règles d’alerte exécutant une recherche enregistrée à intervalles réguliers.  Si les résultats de la requête correspondent aux critères spécifiés, un enregistrement d’alerte est créé et une ou plusieurs actions sont exécutées.  

> [!NOTE]
> À compter du 14 mai 2018, toutes les alertes d’un espace de travail seront automatiquement étendues à Azure. Un utilisateur peut volontairement étendre des alertes à Azure avant le 14 mai 2018. Pour plus d’informations, consultez [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Étendre (copier) des alertes du portail OMS dans Azure). Pour les utilisateurs qui étendent des alertes à Azure, les actions sont désormais contrôlées dans les groupes d’actions Azure. Lorsqu’un espace de travail et ses alertes sont étendus à Azure, vous pouvez récupérer ou ajouter des actions avec [Groupe d’actions - Modèle Azure Resource Manager](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

Les règles d’alerte d’une solution de gestion sont constituées des trois ressources suivantes.

- **Recherche enregistrée.**  Définit la recherche dans les journaux qui est exécutée.  Plusieurs règles d’alerte peuvent partager une même recherche enregistrée.
- **Planification.**  Définit la fréquence d’exécution de la recherche dans les journaux.  Chaque règle d’alerte est associée à une planification unique.
- **Action d’alerte.**  Chaque règle d’alerte est associée à une ressource de groupe d’actions ou à une ressource d’action (héritée) avec un type **Alert**, qui définit les détails de l’alerte, notamment les critères déterminant à quel moment un enregistrement d’alerte est créé, ainsi que la gravité de l’alerte. La ressource [Groupe d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) peut comporter une liste d’actions à effectuer lorsqu’une alerte est déclenchée : appel vocal, SMS, e-mail, webhook, ITSM, runbook automation, application logique, etc.
 
La ressource d’action (héritée) peut éventuellement définir une réponse par e-mail et par runbook.
- **Action webhook (héritée)**  Si la règle d’alerte appelle un webhook, elle nécessite une ressource d’action supplémentaire de type **Webhook**.    

Les ressources de recherche enregistrée sont décrites ci-dessus.  Les autres ressources sont décrites ci-dessous.


### <a name="schedule-resource"></a>Ressource de planification

Une recherche enregistrée peut avoir une ou plusieurs planifications, chacune d’entre elles représentant une règle d’alerte distincte. La planification définit la fréquence à laquelle la recherche est exécutée et l’intervalle de temps pendant lequel les données sont récupérées.  Les ressources de planification ont le type `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` et présentent la structure suivante. Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 


    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }



Les propriétés des ressources de planification sont décrites dans le tableau suivant.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Activé       | OUI | Spécifie si l’alerte est activée lors de sa création. |
| interval      | OUI | La fréquence d’exécution de la requête (en minutes). |
| queryTimeSpan | OUI | Durée sur laquelle les résultats sont évalués (en minutes). |

La ressource de planification doit dépendre de la recherche enregistrée, de sorte qu’elle soit créée avant la planification.

> [!NOTE]
> Le nom d’une planification doit être unique au sein d’un espace de travail. Deux planifications ne peuvent pas avoir le même ID, même si elles sont associées à d’autres recherches enregistrées. De même, pour toutes les recherches enregistrées, planifications et actions créées avec l’API Log Analytics, le nom doit être en minuscules.


### <a name="actions"></a>Actions
Une planification peut avoir plusieurs actions. Une action peut définir un ou plusieurs processus à effectuer, tels que l’envoi d’un courrier électronique ou le démarrage d’un runbook, ou elle peut définir un seuil qui détermine si les résultats d’une recherche satisfont à certains critères.  Certaines actions définissent ces deux aspects afin que les processus soient exécutés quand le seuil est atteint.

Les actions peuvent être définies à l’aide de la ressource [Groupe d’actions] ou de la ressource d’action.

> [!NOTE]
> À compter du 14 mai 2018, toutes les alertes d’un espace de travail seront automatiquement étendues à Azure. Un utilisateur peut volontairement étendre des alertes à Azure avant le 14 mai 2018. Pour plus d’informations, consultez [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Étendre (copier) des alertes du portail OMS dans Azure). Pour les utilisateurs qui étendent des alertes à Azure, les actions sont désormais contrôlées dans les groupes d’actions Azure. Lorsqu’un espace de travail et ses alertes sont étendus à Azure, vous pouvez récupérer ou ajouter des actions avec [Groupe d’actions - Modèle Azure Resource Manager](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).


La propriété **Type** spécifie deux types de ressources d’action.  Une planification nécessite une action **Alert** qui définit les détails de la règle d’alerte et les actions appliquées lors de la création d’une alerte. Les ressources d’action ont un type `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

Les actions d’alerte présentent la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 


```
    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "[variables('Alert').Name]",
            "description": "[variables('Alert').Description]",
            "severity": "[variables('Alert').Severity]",
            "threshold": {
                "operator": "[variables('Alert').Threshold.Operator]",
                "value": "[variables('Alert').Threshold.Value]",
                "metricsTrigger": {
                    "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                    "operator": "[variables('Alert').Trigger.Operator]",
                    "value": "[variables('Alert').Trigger.Value]"
                  },
              },
      "AzNsNotification": {
        "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
        "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
        "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
        }
    }
```

Les propriétés des ressources d’action d’alerte sont décrites dans les tableaux suivants.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| type | OUI | Type de l’action.  **Alert** pour les actions d’alerte. |
| NOM | OUI | Nom d’affichage de l’alerte.  Il s’agit du nom qui s’affiche dans la console pour la règle d’alerte. |
| Description | Non  | La description facultative de l’alerte. |
| Severity | OUI | La gravité de l’enregistrement d’alerte selon les valeurs suivantes :<br><br> **Critical**<br>**Avertissement**<br>**Informational**


#### <a name="threshold"></a>Seuil
Cette section est obligatoire.  Elle définit les propriétés du seuil d’alerte.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Operator | OUI | L’opérateur de comparaison selon les valeurs suivantes :<br><br>**gt = supérieur à<br>lt = inférieur à** |
| Valeur | OUI | La valeur par rapport à laquelle les résultats doivent être comparés. |

##### <a name="metricstrigger"></a>MetricsTrigger
Cette section est facultative.  Vous devez l’inclure pour une alerte relative aux mesures métriques.

> [!NOTE]
> Les alertes relatives aux mesures métriques sont actuellement en version préliminaire publique. 

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| TriggerCondition | OUI | Spécifie si le seuil est défini pour le nombre total de violations ou pour des violations consécutives selon les valeurs suivantes :<br><br>**Total<br>Consecutive** |
| Operator | OUI | L’opérateur de comparaison selon les valeurs suivantes :<br><br>**gt = supérieur à<br>lt = inférieur à** |
| Valeur | OUI | Le nombre de fois où les critères doivent être respectés pour que l’alerte soit déclenchée. |


#### <a name="throttling"></a>Limitation
Cette section est facultative.  Insérez cette section si vous souhaitez supprimer des alertes à partir de la même règle sur une certaine période après la création d’une alerte.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| DurationInMinutes | Oui, si l’élément Throttling est inclus | Le délai de suppression des alertes, en minutes, une fois créée une alerte à partir de la même règle. |


#### <a name="azure-action-group"></a>Groupe d’actions Azure
Toutes les alertes dans Azure, utilisez le groupe d’actions en tant que mécanisme par défaut pour la gestion des actions. Avec le groupe d’actions, vous pouvez spécifier vos actions une seule fois, puis associer le groupe d’actions à plusieurs alertes sur Azure. Vous n’avez plus besoin de déclarer sans cesse les mêmes actions. Les groupes d’actions prennent en charge plusieurs actions, y compris, les e-mails, les SMS, les appels vocaux, les connexions ITSM, les Runbooks Automation, les URI Webhook et bien plus encore. 

Pour les utilisateurs qui ont étendu leurs alertes dans Azure, une planification doit maintenant avoir des informations sur le groupe d’actions transmises avec le seuil, afin de pouvoir créer une alerte. Les informations sur les e-mails, les URLS Webhook, les Runbooks Automation et sur d’autres actions doivent être définies dans un groupe d’actions avant de créer une alerte. Vous pouvez créer un [groupe d’actions à partir d’Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) dans le portail ou utiliser [Groupes d’action - Modèle Resource Manager](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| AzNsNotification | OUI | ID de ressource du groupe d’actions Azure à associer à l’alerte pour entreprendre les actions nécessaires lorsque les critères d’alerte sont remplis. |
| CustomEmailSubject | Non  | Ligne d’objet personnalisée de l’e-mail envoyé à toutes les adresses spécifiées dans le groupe d’actions associé. |
| CustomWebhookPayload | Non  | Charge utile personnalisée à envoyer à tous les points de terminaison Webhook définis dans le groupe d’actions associé. Le format dépend de ce qu’attend le Webhook, et doit être un JSON sérialisé valide. |


#### <a name="actions-for-oms-legacy"></a>Actions pour OMS (héritées)

Chaque planification est associée à une action **Alert**.  Ceci définit les détails de l’alerte et, éventuellement, les actions de notification et de correction.  Une notification envoie un e-mail à une ou plusieurs adresses.  Une correction démarre un runbook dans Azure Automation pour tenter de résoudre le problème détecté.

> [!NOTE]
> À compter du 14 mai 2018, toutes les alertes d’un espace de travail seront automatiquement étendues à Azure. Un utilisateur peut volontairement étendre des alertes à Azure avant le 14 mai 2018. Pour plus d’informations, consultez [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Étendre (copier) des alertes du portail OMS dans Azure). Pour les utilisateurs qui étendent des alertes à Azure, les actions sont désormais contrôlées dans les groupes d’actions Azure. Lorsqu’un espace de travail et ses alertes sont étendus à Azure, vous pouvez récupérer ou ajouter des actions avec [Groupe d’actions - Modèle Azure Resource Manager](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Cette section est facultative. Insérez-la si vous souhaitez que l’alerte envoie un e-mail à un ou plusieurs destinataires.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Destinataires | OUI | Liste des adresses e-mail (séparées par des virgules) auxquelles une notification est envoyée quand une alerte est créée, comme dans l’exemple suivant.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Objet | OUI | La ligne d’objet du message. |
| Pièce jointe | Non  | Actuellement, les pièces jointes ne sont pas prises en charge.  Si cet élément est inclus, il doit avoir la valeur **None**. |


##### <a name="remediation"></a>Correction
Cette section est facultative. Insérez-la si vous souhaitez qu’un runbook démarre en réponse à l’alerte. |

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| RunbookName | OUI | Le nom du runbook à démarrer. |
| WebhookUri | OUI | L’URI du webhook pour le runbook. |
| Expiry | Non  | La date et l’heure d’expiration de la correction. |

##### <a name="webhook-actions"></a>Actions de webhook

Les actions de webhook démarrent un processus en appelant une URL et, éventuellement, en fournissant une charge utile à envoyer. Elles sont similaires aux actions correctives, à la différence qu’elles sont destinées à des webhooks qui peuvent appeler des processus autres que des runbooks Azure Automation. En outre, elles permettent de fournir une charge utile à remettre au processus distant.

Si votre alerte appelle un webhook, elle nécessite une ressource d’action avec un type **Webhook** en plus de la ressource d’action **Alerte**.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

Les propriétés des ressources d’action Webhook sont décrites dans les tableaux suivants.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Type | OUI | Type de l’action.  **Webhook** pour les actions de webhook. |
| Nom | OUI | Le nom d’affichage de l’action.  Il n’est pas affiché dans la console. |
| wehookUri | OUI | L’URI du webhook. |
| customPayload | Non  | Charge utile personnalisée à envoyer au webhook. Le format dépend de ce que le webhook attend. |


## <a name="sample"></a>Exemple

L’exemple de solution ci-après inclut les ressources suivantes :

- Recherche enregistrée
- Planification
- Groupe d’actions

L’exemple utilise des variables de [paramètres de solution standard](operations-management-suite-solutions-solution-file.md#parameters) qui seraient généralement utilisées dans une solution, par opposition aux valeurs de codage en dur dans les définitions de ressource.

```
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "actiongroup": {
            "type": "string",
            "metadata": {
              "Description": "List of action groups for alert actions separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-03-20",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
              "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
              "Interval": 15,
              "TimeSpan": 60
            },
            "MetricsTrigger": {
              "TriggerCondition": "Consecutive",
              "Operator": "gt",
              "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
              "GroupIds": [
                "[parameters('actiongroup')]"
              ],
              "CustomEmailSubject": "Sample alert"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
              ]
            },
            "plan": {
              "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
              "Version": "[variables('SolutionVersion')]",
              "product": "[variables('ProductName')]",
              "publisher": "[variables('SolutionPublisher')]",
              "promotionCode": ""
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
              "etag": "*",
              "query": "[variables('MySearch').query]",
              "displayName": "[variables('MySearch').displayName]",
              "category": "[variables('MySearch').category]"
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
              "etag": "*",
              "interval": "[variables('MyAlert').Schedule.Interval]",
              "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
              "enabled": true
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Alert",
              "Name": "[variables('MyAlert').DisplayName]",
              "Description": "[variables('MyAlert').Description]",
              "Severity": "[variables('MyAlert').Severity]",
              "Threshold": {
                "Operator": "[variables('MyAlert').ThresholdOperator]",
                "Value": "[variables('MyAlert').ThresholdValue]",
                "MetricsTrigger": {
                  "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                  "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                  "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                }
              },
              "Throttling": {
                "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
              },
            "AzNsNotification": {
              "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
              "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
            }             
            }
          }
        ]
    }
```

Le fichier de paramètres suivant fournit des exemples de valeurs pour cette solution.
```
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspacename": {
                "value": "myWorkspace"
            },
            "accountName": {
                "value": "myAccount"
            },
            "workspaceregionId": {
                "value": "East US"
            },
            "regionId": {
                "value": "East US 2"
            },
            "pricingTier": {
                "value": "Free"
            },
            "actiongroup": {
                "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
            }
        }
    }
```

## <a name="next-steps"></a>Étapes suivantes
* [Ajoutez des vues](operations-management-suite-solutions-resources-views.md) à votre solution de gestion.
* [Ajoutez des runbooks Automation et d’autres ressources](operations-management-suite-solutions-resources-automation.md) à votre solution de gestion.

