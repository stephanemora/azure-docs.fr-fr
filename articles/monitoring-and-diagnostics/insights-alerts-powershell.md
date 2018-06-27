---
title: Utiliser PowerShell pour créer des alertes classiques pour les services Azure | Microsoft Docs
description: Déclenchez des e-mails ou des notifications, ou appelez des URL de sites web (webhooks) ou déclenchez une automatisation lorsque les conditions spécifiées sont remplies.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286197"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Utiliser PowerShell pour créer des alertes pour les services Azure
> [!div class="op_single_selector"]
> * [Portail](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> Cet article décrit comment créer des alertes de métriques classiques plus anciennes. Désormais, Azure Monitor prend en charge des [alertes de métriques plus récentes et plus performantes](monitoring-near-real-time-metric-alerts.md). Ces alertes peuvent surveiller plusieurs métriques, et permettent d’obtenir des alertes sur des métriques dimensionnelles. La prise en charge de PowerShell pour les alertes de métriques plus récentes sera bientôt disponible.
>
>

Cet article vous montre comment configurer des alertes de métriques classiques Azure à l’aide de PowerShell.  

Vous pouvez recevoir des alertes en fonction des métriques pour vos services Azure, ou vous pouvez recevoir des alertes pour les événements qui se produisent dans Azure.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois quand la condition est remplie et une fois que la condition n’est plus remplie.    
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou lorsqu’un événement particulier se produit. Pour en savoir plus sur les alertes de journal d’activité, consultez [Créer des alertes de journal d’activité (classiques)](monitoring-activity-log-alerts.md).

Vous pouvez configurer une alerte de métrique classique pour effectuer les tâches suivantes lors de son déclenchement :

* Envoyer des notifications par e-mail à l’administrateur du service et aux coadministrateurs.
* Envoyer un e-mail à d’autres adresses e-mail que vous spécifiez.
* Appeler un webhook.
* Démarrer l’exécution d’un runbook Azure (uniquement à partir du portail Azure).

Vous pouvez configurer et obtenir des informations sur les règles d’alerte de métrique classique à partir des emplacements suivants : 

* [Portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Pour obtenir des informations complémentaires, vous pouvez à tout moment saisir ```Get-Help``` suivi de la commande PowerShell sur laquelle vous avez besoin d’aide.

## <a name="create-alert-rules-in-powershell"></a>Créer des règles d’alerte dans PowerShell
1. Connectez-vous à Azure.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Récupérez la liste des abonnements dont vous disposez. Vérifiez que vous travaillez avec le bon abonnement. Si ce n’est pas le cas, récupérez le bon abonnement à l’aide de la sortie de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Répertoriez les règles existantes sur un groupe de ressources à l’aide de la commande suivante :

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Pour créer une règle, vous avez d’abord besoin de différentes informations importantes.

    - L’**ID de la ressource** pour laquelle vous souhaitez définir une alerte.
    - Les **définitions de métriques** qui sont disponibles pour cette ressource.

     Pour obtenir l’ID de la ressource, vous pouvez utiliser le portail Azure. À supposer que la ressource ait déjà été créée, sélectionnez-la dans le portail. Puis, dans le panneau suivant, dans la section **Paramètres**, sélectionnez **Propriétés**. **ID DE RESSOURCE** est un champ du panneau suivant. 
     
     Vous pouvez également obtenir l’ID de la ressource à l’aide d’[Azure Resource Explorer](https://resources.azure.com/).

     Voici un exemple d’ID de ressource d’une application web :

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Vous pouvez utiliser `Get-AzureRmMetricDefinition` pour afficher la liste de toutes les définitions de métriques d’une ressource spécifique :

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     L’exemple suivant génère une table avec le nom de la métrique et son unité :

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Pour obtenir la liste complète des options disponibles pour Get-AzureRmMetricDefinition, exécutez `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. L’exemple suivant configure une alerte sur une ressource de site web. L’alerte se déclenche chaque fois qu’il reçoit constamment du trafic pendant cinq minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant cinq minutes.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Pour créer un webhook ou envoyer un e-mail lorsqu’une alerte se déclenche, commencez par créer le message et/ou le webhook. Puis créez la règle immédiatement après avec la balise -Actions, comme indiqué dans l’exemple suivant. Vous ne pouvez pas associer des webhooks ou e-mails à des règles qui ont déjà été créées.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Examinez les règles individuelles pour vérifier que vos alertes ont été correctement créées.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Supprimez vos alertes. Ces commandes suppriment les règles qui ont été créées précédemment dans cet article.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](monitoring-overview.md), notamment les types d’informations que vous pouvez collecter et surveiller.
* Découvrez comment [configurer des Webhooks dans les alertes](insights-webhooks-alerts.md).
* Découvrez comment [configurer des alertes sur des événements de journal d’activité](monitoring-activity-log-alerts.md).
* En savoir plus sur les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble de la collecte des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) pour collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
