---
title: Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor
description: Utilisez Azure Monitor pour créer, afficher et gérer des règles d’alerte de journal dans Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6e6db7ef2f2de075aea41a29e69b2c674efafa17
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635354"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor  

## <a name="overview"></a>Vue d’ensemble
Cet article explique comment configurer des alertes de journal à l’aide de l’interface d’alertes dans le portail Azure. La définition d’une règle d’alerte se fait en trois parties :
- Cible : ressource Azure spécifique à surveiller
- Critères : condition spécifique ou logique qui, une fois détectée (signal), doit déclencher une action
- Action : appel spécifique est envoyé au récepteur d’une notification (courrier électronique, SMS, webhook, etc.)

Le terme **alertes de journal** décrit les alertes où le signal est une requête personnalisée basée sur [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Application Insights](../application-insights/app-insights-analytics.md). Obtenez plus d’informations sur la fonctionnalité, la terminologie et les types dans [Alertes de journal - Vue d’ensemble](monitor-alerts-unified-log.md).

> [!NOTE]
> Les données de journal populaires d’[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) sont désormais également disponibles sur la plateforme de métrique dans Azure Monitor. Pour la vue de détails, consultez [Alerte de métrique pour les journaux](monitoring-metric-alerts-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Gestion des alertes de journal à partir du portail Azure

Vous trouverez ci-après un guide pas à pas sur l’utilisation des alertes de journal à l’aide de l’interface du portail Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Créer une règle d’alerte de journal avec le portail Azure
1. Dans le [portail](https://portal.azure.com/), sélectionnez **Surveiller** et choisissez **Alertes** dans la section SURVEILLER.  
    ![Surveillance](media/alert-log/AlertsPreviewMenu.png)

1. Cliquez sur le bouton **Nouvelle règle d’alerte** pour créer une alerte dans Azure.
    ![Ajouter une alerte](media/alert-log/AlertsPreviewOption.png)

1. La section Créer une alerte s’affiche avec ses trois parties : *définition de la condition d’alerte*, *définition des détails de l’alerte* et *définition du groupe d’actions*.

    ![Créer une règle](media/alert-log/AlertsPreviewAdd.png)

1.  Définissez la condition d’alerte en utilisant le lien **Sélectionner une ressource**, puis en spécifiant la cible via la sélection d’une ressource. Filtrez en choisissant l’_abonnement_, le _type de ressource_ et la _ressource_ nécessaire. 

    >[!NOTE]

    > Pour créer une alerte de journal, avant de continuer, vérifiez le signal de **journal** disponible pour la ressource sélectionnée.
    ![Sélectionner une ressource](media/alert-log/Alert-SelectResourceLog.png)

 
1. *Alertes de journal* : assurez-vous que le **Type de ressource** est une source analytique telle que *Log Analytics* ou *Application Insights* et le type de signal **Journal**. Ensuite, une fois la **ressource** appropriée choisie, cliquez sur le bouton *Terminé*. Utilisez ensuite le bouton **Ajouter des critères** pour afficher la liste des options de signal disponibles pour la ressource et à partir de l’option **Recherche de journal personnalisée** de la liste de signaux pour le service de surveillance des journaux choisi, tel que *Log Analytics* ou *Application Insights*.

   ![Sélectionner une ressource : recherche de journal personnalisée](media/alert-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Les listes d’alertes peuvent importer une requête analytique en tant que type de signal : **Journal (requête enregistrée)**, comme indiqué dans l’illustration ci-dessus. Les utilisateurs peuvent donc perfectionner votre requête dans Analytics et l’enregistrer pour l’utiliser ultérieurement dans Alerts. Pour en savoir plus sur l’enregistrement de requêtes, consultez la section relative à [l’utilisation des recherches de journaux dans Log Analytics](../log-analytics/log-analytics-queries.md) ou aux [requêtes partagées dans Application Insights Analytics](../log-analytics/log-analytics-queries.md). 

1.  *Alertes de journal* : une fois sélectionnée, la requête de génération d’alerte peut être indiquée dans le champ **Requête de recherche**. Si la syntaxe de la requête est incorrecte, le champ affiche le message d’erreur en rouge. Si la syntaxe de la requête est correcte, les données de la requête indiquée sont indiquées à titre de référence sous forme de graphique avec la possibilité d’ajuster la fenêtre de temps entre les six dernières heures et la semaine précédente.

 ![Configurer une règle d’alerte](media/alert-log/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > La visualisation des données historiques n’est possible que si les résultats de la requête comportent des informations temporelles. Si votre requête produit des données synthétisées ou des valeurs de colonnes spécifiques, les mêmes informations sont indiquées sous forme de tracé unique.

    >  Pour le type de mesure de métrique d’alertes de journal utilisant Application Insights, vous pouvez spécifier la variable à utiliser pour regrouper les données à l’aide de l’option **Agréger sur**, comme illustré ci-dessous :

    ![Option Agréger sur](media/alert-log/aggregate-on.png)

1.  *Alertes de journal* : avec la visualisation en place, la **logique d’alerte** peut être sélectionnée parmi les options proposées de condition, d’agrégation et de seuil. Enfin, dans la logique, spécifiez le moment auquel évaluer la condition indiquée à l’aide de l’option **Période**. Précisez également la fréquence d’exécution de l’alerte en sélectionnant **Fréquence**.
Les **alertes de journal** peuvent reposer sur les éléments suivants :
   - *Nombre d’enregistrements* : une alerte est créée si le nombre d’enregistrements renvoyés par la requête est supérieur ou inférieur à la valeur indiquée.
   - *Mesure de métrique* : une alerte est créée si chaque *valeur d’agrégation* dans les résultats dépasse la valeur de seuil indiquée et s’il existe un *regroupement* selon la valeur choisie. Le nombre de violations d’une alerte est le nombre de fois où que le seuil est dépassé pendant la période choisie. Vous pouvez spécifier le nombre total de violations pour obtenir toutes les combinaisons de violations dans les résultats ou les violations consécutives pour exiger que les violations aient lieu dans des échantillons consécutifs. Apprenez-en davantage sur les [alertes de journal et leurs types](monitor-alerts-unified-log.md).


1. Ensuite, définissez un nom pour votre alerte dans le champ **Nom de la règle d’alerte** avec une **description** détaillant les spécificités de l’alerte et une valeur de **gravité** choisie parmi les options fournies. Ces détails sont réutilisés dans l’ensemble des courriers électroniques d’alerte, notifications ou messages Push d’Azure Monitor. En outre, l’utilisateur peut activer immédiatement la règle d’alerte lors de la création via l’option **Activer la règle lors de sa création**.

    Pour les **alertes de journal** uniquement, certaines fonctionnalités supplémentaires sont disponibles dans les détails d’alerte :

    - **Supprimer des alertes** : quand vous activez la suppression de la règle d’alerte, les actions correspondant à la règle sont désactivées pour une durée définie après la création d’une alerte. La règle est toujours en cours d’exécution et crée des enregistrements d’alerte si les critères sont satisfaits. Cela vous donne le temps de résoudre le problème sans exécuter des actions en double.

        ![Supprimer des alertes de journal](media/alert-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Spécifiez une valeur Supprimer l’alerte supérieure à la fréquence de l’alerte pour garantir l’arrêt des notifications sans chevauchement

1. Enfin, le cas échéant, spécifiez le **groupe d’actions** à déclencher pour la règle d’alerte lorsque la condition d’alerte est remplie. Vous pouvez choisir n’importe quel groupe d’actions existant avec l’alerte ou créer un autre groupe d’actions. En fonction du groupe d’actions sélectionné, lors du déclenchement de l’alerte, Azure envoie des courriers électroniques, envoie des SMS, appelle des webhooks, corrige le problème à l’aide de runbooks Azure, envoie un message Push à votre outil ITSM, etc. En savoir plus sur les [groupes d’actions](monitoring-action-groups.md).

    > [!NOTE]
    > Consultez la [limites du service d’abonnement Azure](../azure-subscription-service-limits.md) pour connaître les limites de charges utiles de Runbook déclenchées pour les alertes de journal par le biais de groupes d’actions Azure

    Pour les **alertes de journal**, certaines fonctionnalités supplémentaires sont disponibles afin de passer outre les actions par défaut :

    - **Notification par courrier électronique** : substitue *l’objet du message électronique* dans le message, envoyé via le groupe d’actions, si une ou plusieurs actions de messagerie existent dans ledit groupe d’actions. Vous ne pouvez pas modifier le corps du message et ce champ n’est **pas** destiné à l’adresse de messagerie.
    - **Inclure la charge utile Json personnalisée** : remplace le JSON webhook utilisé par les groupes d’actions, si une ou plusieurs actions de webhook existent dans ledit groupe d’actions. L’utilisateur peut spécifier le format JSON à utiliser pour tous les webhooks configurés dans le groupe d’actions associé ; Pour plus d’informations sur les formats de webhook, voir [Action webhook pour les alertes de journal](monitor-alerts-unified-log-webhook.md). L’option d’affichage de Webhook est fournie pour vérifier le format à l’aide d’exemples de données JSON.

        ![Remplacements d’actions pour les alertes de journal](media/alert-log/AlertsPreviewOverrideLog.png)


1. Si tous les champs sont valides et qu’une coche verte est visible, le bouton **Créer une règle d’alerte** peut être activé. Une alerte est alors créée dans Azure Monitor – Alertes. Toutes les alertes peuvent être consultées dans le tableau de bord des alertes.

    ![Création de règles](media/alert-log/AlertsPreviewCreate.png)

    Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

Les utilisateurs peuvent également finaliser leur requête d’analyse dans la [page Log Analytics du portail Azure](../log-analytics/log-analytics-log-search-portals.md#log-analytics-page
), l’envoyer (push) afin de créer une alerte à l’aide du bouton « Définir l’alerte », puis suivre les instructions à partir de l’étape 6 du didacticiel ci-dessus.

 ![Log Analytics - Définir l’alerte](media/alert-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Afficher et gérer les alertes de journal dans le portail Azure

1. Dans le [portail](https://portal.azure.com/), sélectionnez **Surveiller** et choisissez **Alertes** dans la section SURVEILLER.  

1. Le **tableau de bord Alertes**, dans lequel toutes les alertes Azure (alertes de journal incluses) sont affichées dans un même tableau, y compris toutes les instances pendant lesquelles votre règle d’alerte de journal a été déclenchée, s’affiche. Pour en savoir plus, consultez [Gestion des alertes](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Les règles d’alerte de journal incluent une logique personnalisée basée sur une requête fournie par les utilisateurs et, par conséquent, sans un état résolu. Chaque fois que les conditions spécifiées dans la règle d’alerte de journal sont remplies, celle-ci est déclenchée. 


1. Cliquez sur le bouton **Gérer les règles** dans la barre supérieure pour accéder à la section de gestion des règles, où toutes les règles d’alerte créées sont répertoriées, y compris les alertes désactivées.
    ![ Gérer les règles d’alerte](media/alert-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gestion des alertes de journal à l’aide du modèle de ressource Azure
Des alertes de journal peuvent actuellement être créées à l’aide de deux modèles de ressources différents en fonction de la plateforme d’analyse sur laquelle l’alerte doit se trouver : Log Analytics ou Application Insights.

La section ci-dessous fournit ainsi des détails sur l’utilisation du modèle de ressource pour les alertes de journal de chaque plateforme d’analyse.

### <a name="azure-resource-template-for-log-analytics"></a>Modèle de ressource Azure pour Log Analytics
Les alertes de journal pour Log Analytics sont créées par le biais de règles d’alerte exécutant une recherche enregistrée à intervalles réguliers. Si les résultats de la requête correspondent aux critères spécifiés, un enregistrement d’alerte est créé et une ou plusieurs actions sont exécutées. 

Les modèles de ressources pour la recherche Log Analytics enregistrée et les alertes Log Analytics sont disponibles dans la section Log Analytics de la documentation. Pour en savoir plus, consultez l’article [Ajout de recherches et d’alertes enregistrées Log Analytics](../azure-monitor/insights/solutions-resources-searches-alerts.md) qui contient des exemples illustratifs et des détails sur le schéma.

### <a name="azure-resource-template-for-application-insights"></a>Modèle de ressource Azure pour Application Insights
Les alertes de journal pour les ressources Application Insights ont le type `Microsoft.Insights/scheduledQueryRules/`. Pour plus d’informations sur ce type de ressource, consultez [Azure Monitor - Référence de l’API des règles de requêtes planifiées](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

Voici la structure pour un modèle de ressource basé sur la [création de règles de requêtes planifiées](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate), avec un exemple de jeu de données en tant que variables.

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
> Le champ Tag avec hidden-link vers la ressource cible est obligatoire en cas d’utilisation d’appel d’API de [règles de requêtes planifiées](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) ou de modèle de ressource. 

L’exemple json ci-dessus peut par exemple être enregistré en tant que sampleScheduledQueryRule.json pour les besoins de cette procédure pas à pas, et peut être déployé à l’aide d’[Azure Resource Manager dans le portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>Gestion des alertes de journal à l’aide de PowerShell, de la CLI ou d’une API
Des alertes de journal peuvent actuellement être créées à l’aide de deux API compatibles avec Resource Manager différentes en fonction de la plateforme d’analyse sur laquelle l’alerte doit se trouver : Log Analytics ou Application Insights.

La section ci-dessous fournit ainsi des détails sur l’utilisation d’une API via PowerShell ou la CLI pour les alertes de journal de chaque plateforme d’analyse.

### <a name="powershell-cli-or-api-for-log-analytics"></a>PowerShell, CLI ou API pour Log Analytics
L’API REST d’alerte Log Analytics est un service RESTful qui est accessible par le biais de l’API REST Azure Resource Manager. L’API est ainsi accessible à partir d’une ligne de commande PowerShell et génère pour vous des résultats de recherche au format JSON, ce qui vous permet d’utiliser ces résultats, par programmation, de différentes manières.

Pour en savoir plus, consultez l’article [Créer et gérer des règles d’alerte dans Log Analytics avec l’API REST](../azure-monitor/platform/api-alerts.md), qui contient notamment des exemples d’accès à l’API à partir de Powershell.

### <a name="powershell-cli-or-api-for-application-insights"></a>PowerShell, CLI ou API pour Application Insights
L’[API Azure Monitor - Règles de requêtes planifiées](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) est une API REST entièrement compatible avec l’API REST Azure Resource Manager. Elle peut donc être utilisée par le biais de Powershell à l’aide d’applets de commande Resource Manager en plus d’Azure CLI.

Vous trouverez ci-dessous une illustration de l’utilisation par le biais de l’applet de commande Azure Resource Manager PowerShell pour l’exemple de modèle de ressource présenté plus haut (sampleScheduledQueryRule.json) dans la [section Modèle de ressource](#azure-resource-template-for-application-insights) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Vous trouverez ci-dessous une illustration de l’utilisation par le biais de la commande Azure Resource Manager dans Azure CLI pour l’exemple de modèle de ressource présenté plus haut (sampleScheduledQueryRule.json) dans la [section Modèle de ressource](#azure-resource-template-for-application-insights) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Une fois l’opération réussie, le code 201 est retourné pour signaler la création de la nouvelle règle d’alerte, ou l’état 200 si une règle d’alerte existante a été modifiée.


  
## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [alertes de journal dans les alertes Azure ](monitor-alerts-unified-log.md)
* Comprendre les [actions Webhook pour les alertes de journal](monitor-alerts-unified-log-webhook.md)
* En savoir plus sur [Application Insights](../application-insights/app-insights-analytics.md)
* En savoir plus sur [Log Analytics](../log-analytics/log-analytics-queries.md). 

