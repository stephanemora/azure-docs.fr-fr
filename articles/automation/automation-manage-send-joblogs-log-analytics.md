---
title: Transférer des données de travaux Azure Automation aux journaux d’activité Azure Monitor
description: Cet article montre comment envoyer des flux de travail runbook et l’état du travail dans les journaux Azure Azure Monitor pour fournir une gestion et obtenir des informations supplémentaires.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82baef7ce0d91713c8bef202ab0ea0925d290f3a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496588"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Transférer l’état du travail et flux de travail d’Automation dans les journaux d’Azure Monitor

Automation peut envoyer un état de tâche du runbook et des flux de tâches à votre espace de travail Log Analytics. Ce processus n’implique aucune liaison d’espace de travail et est complètement indépendant. Les journaux d’activité de travail et les flux de travail sont visibles dans le portail Azure, ou avec PowerShell, pour des travaux individuels. Cela vous permet d’effectuer des enquêtes simples. Désormais avec les journaux Azure Monitor, vous pouvez :

* Obtenir des informations sur vos travaux Automation.
* Déclencher un e-mail ou une alerte en fonction du statut de votre travail de runbook (par exemple, échec ou état suspendu).
* Écrire des requêtes avancées sur plusieurs flux de travaux.
* Mettre en corrélation des travaux sur différents comptes Automation.
* Visualiser votre historique des travaux dans le temps.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Conditions préalables et considérations relatives au déploiement

Pour commencer à envoyer vos journaux Automation dans les journaux d’Azure Monitor, vous devez :

* La dernière version de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Un espace de travail Log Analytics. Pour plus d’informations, consultez [prise en main Azure Monitor journaux](../log-analytics/log-analytics-get-started.md). 
* L’ID de ressource de votre compte Azure Automation.

Pour trouver l’ID de ressource de votre compte Azure Automation :

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Pour trouver l’ID de ressource de votre espace de travail Log Analytics, exécutez la commande PowerShell suivante :

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Si vous possédez plusieurs comptes Automation ou plusieurs espaces de travail, recherchez le *Nom* à configurer dans la sortie des commandes précédentes et copiez la valeur de *ResourceId*.

Pour rechercher Le *Nom* de votre compte Automation, sélectionnez votre compte Automation à partir du panneau **Compte Automation** du portail Azure, puis sélectionnez **Tous les paramètres**. Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**.  Dans le panneau **Propriétés**, vous pouvez observer ces valeurs.<br> ![Propriétés du compte Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurer l’intégration avec les journaux d’Azure Monitor

1. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer**.
2. Exécutez la commande PowerShell suivante, et remplacez la valeur de `[your resource id]` et de `[resource id of the log analytics workspace]` par celles de l’étape précédente.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Après avoir exécuté ce script, il peut prendre une heure avant de commencer à voir les enregistrements dans les journaux Azure Monitor de nouveaux JobLogs ou JobStreams.

Pour afficher les journaux, exécutez la requête suivante dans la recherche de journal log analytique : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Vérifier la configuration

Pour vous assurer que votre compte Automation envoie des journaux d’activité à votre espace de travail Log Analytics, vérifiez que les diagnostics sont correctement configurés sur le compte Automation à l’aide de la commande PowerShell suivante :

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Dans la sortie, assurez-vous que :

* Sous *Journaux d’activité*, la valeur de *Activé* est *True*.
* La valeur de *WorkspaceId* est définie sur l’ID de ressource de votre espace de travail Log Analytics.

## <a name="azure-monitor-log-records"></a>Enregistrements de journaux Azure Monitor

Diagnostic d’Azure Automation crée deux types d’enregistrements dans les journaux Azure Monitor et sont marqué comme **AzureDiagnostics**. Les requêtes suivantes utilisent le langage de requête mis à niveau dans les journaux d’Azure Monitor. Pour plus d’informations sur les requêtes courantes entre le langage de requête hérité et le nouveau langage de requête Azure Kusto visitez [hérité au nouveau aide-mémoire de langage de requête Kusto Azure](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Journaux d’activité de travail

| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution du travail du runbook. |
| RunbookName_s |Nom du runbook. |
| Caller_s |Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID représentant l’ID du travail du runbook. |
| ResultType |L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Nouveau<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Terminé |
| Catégorie | Classification du type de données. Pour Automation, la valeur est JobLogs. |
| OperationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job. |
| Ressource | Nom du compte Automation |
| SourceSystem | Fonctionnement des journaux Azure Monitor collecte des données. Toujours *Azure* pour les diagnostics Azure. |
| resultDescription |Décrit l’état résultant du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed |
| CorrelationId |GUID représentant l’ID de corrélation du travail du runbook. |
| ResourceId |Spécifie l’id de ressource de compte Azure Automation du runbook. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Flux de travail
| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution du travail du runbook. |
| RunbookName_s |Nom du runbook. |
| Caller_s |Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| StreamType_s |Type de flux de travail. Les valeurs possibles sont les suivantes :<br>progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID représentant l’ID du travail du runbook. |
| ResultType |L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>- In Progress |
| Catégorie | Classification du type de données. Pour Automation, la valeur est JobStreams. |
| OperationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job. |
| Ressource | Nom du compte Automation |
| SourceSystem | Fonctionnement des journaux Azure Monitor collecte des données. Toujours *Azure* pour les diagnostics Azure. |
| resultDescription |Inclut le flux de sortie du runbook. |
| CorrelationId |GUID représentant l’ID de corrélation du travail du runbook. |
| ResourceId |Spécifie l’id de ressource de compte Azure Automation du runbook. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Affichage des journaux Automation dans les journaux Azure Monitor

Maintenant que vous commencé à envoyer vos journaux de travaux Automation dans les journaux d’Azure Monitor, nous allons voir ce que vous pouvez faire avec ces journaux dans les journaux Azure Monitor.

Pour afficher les journaux d’activité, exécutez la requête suivante :`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envoyer un e-mail en cas d’échec ou de suspension d’un travail de runbook
Nos clients demandent souvent à pouvoir envoyer un e-mail ou un SMS lorsqu’une erreur survient sur un travail de runbook.   

Pour créer une règle d’alerte, vous devez commencer par créer une recherche de journal pour les enregistrements de travaux de runbook qui doivent appeler l’alerte. Cliquez sur le bouton **Alerte** pour créer et configurer la règle d’alerte.

1. Dans la page de vue d’ensemble d’espace de travail Analytique de journal, cliquez sur **afficher les journaux**.
2. Créez une requête de recherche dans les journaux pour votre alerte en tapant la recherche suivante dans le champ de requête : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`. Vous pouvez également effectuer un regroupement par nom de runbook à l’aide de `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`.

   Si vous avez configuré des journaux d’activité dans votre espace de travail sur plusieurs abonnements ou comptes Automation, vous pouvez également regrouper vos alertes par abonnement ou par compte Automation. Le nom du compte Automation se trouve dans le champ Ressource de la recherche de JobLogs.
3. Pour ouvrir l’écran **Créer une règle**, cliquez sur **+ Nouvelle règle d’alerte** en haut de la page. Pour plus d’informations sur les options de configuration de l’alerte, voir [Alertes de journaux dans Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Rechercher tous les travaux qui ont rencontré des erreurs
En plus des alertes concernant les échecs, vous pouvez déterminer lorsqu’une tâche de runbook comporte une erreur sans fin d’exécution. Dans ce cas, PowerShell génère un flux d’erreur. Toutefois, les erreurs sans fin d’exécution ne provoquent la suspension ou l’échec du travail.    

1. Dans votre espace de travail Analytique de journal, cliquez sur **journaux**.
2. Dans le champ de requête, tapez `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`, puis cliquez sur le bouton **Rechercher**.

### <a name="view-job-streams-for-a-job"></a>Afficher les flux de travail pour un travail
Lorsque vous déboguez un travail, vous pouvez également examiner les flux de travaux. La requête ci-dessous montre tous les flux pour un seul travail avec le GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 :   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Afficher l’état de travail historique
Vous pouvez enfin souhaiter visualiser l’historique de vos travaux dans le temps. Vous pouvez utiliser cette requête pour rechercher l’état de vos travaux au fil du temps.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Graphique de l’état de la tâche historique de Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Supprimer les paramètres de diagnostic

Pour supprimer les paramètres de diagnostic du compte Automation, exécutez les commandes suivantes :

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Résumé

En envoyant vos données d’état et de flux de travaux Automation dans les journaux d’Azure Monitor, vous pouvez obtenir plus de détails sur l’état de vos travaux Automation en :
+ Configurant des alertes pour vous avertir en cas de problème.
+ Utilisant des vues et des requêtes de recherche personnalisées pour visualiser les résultats de votre runbook, l’état du travail de runbook et d’autres indicateurs ou mesures clés associées.  

Les journaux d’analyse Azure fournit une meilleure visibilité opérationnelle sur vos travaux Automation et peut aider à traiter les incidents plus rapides.  

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la façon de construire différentes requêtes de recherche et passez en revue les journaux de travaux Automation avec les journaux d’Azure Monitor, consultez [recherches de journal dans les journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Pour savoir comment créer et récupérer la sortie et les messages d’erreur de runbooks, consultez la page [Sortie et messages de runbooks](automation-runbook-output-and-messages.md).
* Pour plus d’informations sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et autres détails techniques, voir [Suivi d’une tâche de runbook](automation-runbook-execution.md).
* Pour en savoir plus sur les journaux d’Azure Monitor et les sources de collecte de données, consultez [présentation des journaux de collecte stockage des données Azure dans Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).

