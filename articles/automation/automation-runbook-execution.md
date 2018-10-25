---
title: Exécution d'un Runbook dans Azure Automation
description: Décrit les détails du traitement d'un Runbook dans Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b1a6e2921fdaf9ede1184cfc02c3f61f63c60ac
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393760"
---
# <a name="runbook-execution-in-azure-automation"></a>Exécution d'un Runbook dans Azure Automation

Lorsque vous démarrez un Runbook dans Azure Automation, une tâche est créée. Une tâche est une instance d'exécution unique d'un Runbook. Un travail Azure Automation est assigné pour exécuter chaque tâche. Même si les travaux sont partagés par de nombreux comptes Azure, les tâches des différents comptes Automation sont isolées les unes des autres. Vous n’avez pas le contrôle du travail qui traite la requête de votre tâche. Un même runbook peut avoir beaucoup de tâches qui s’exécutent simultanément. L’environnement d’exécution pour les travaux du même compte Automation peut être réutilisé. Lorsque vous affichez la liste des Runbooks du portail Azure, vous voyez l'état de toutes les tâches démarrées pour chaque Runbook. Vous pouvez afficher la liste des tâches de chaque runbook pour en assurer le suivi de l’état. Pour obtenir une description des différents états des tâches, consultez [États des tâches](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Le diagramme suivant illustre le cycle de vie d’une tâche de Runbook pour des [Runbooks graphiques](automation-runbook-types.md#graphical-runbooks) et des [Runbooks de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![États des tâches - Workflow PowerShell](./media/automation-runbook-execution/job-statuses.png)

Le diagramme suivant illustre le cycle de vie d'une tâche de Runbook pour des [Runbooks PowerShell](automation-runbook-types.md#powershell-runbooks).

![États des tâches - Script PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Vos tâches auront accès à vos ressources Azure en créant une connexion à votre abonnement Azure. Ils auront uniquement accès aux ressources de votre centre de données si ces ressources sont accessibles depuis le cloud public.

## <a name="job-statuses"></a>États des tâches

Le tableau suivant décrit les différents statuts possibles pour une tâche. PowerShell a deux types d’erreurs : les erreurs avec fin d’exécution et les erreurs sans fin d’exécution. Si des erreurs avec fin d’exécution se produisent, l’état du runbook passe à **Failed** (Échec). Si des erreurs sans fin d’exécution se produisent, le script continue de s’exécuter. Par exemple, une erreur sans fin d’exécution peut se produire quand l’applet de commande `Get-ChildItem` est utilisée avec un chemin qui n’existe pas. PowerShell détecte que le chemin n’existe pas, il génère une erreur et il passe au dossier suivant. Cette erreur ne met pas le runbook à l’état **Failed** (Échec) et peut le marquer comme **Completed** (Terminé). Pour forcer l’arrêt d’un runbook après une erreur sans fin d’exécution, utilisez `-ErrorAction Stop` avec l’applet de commande.

| Statut | Description |
|:--- |:--- |
| Completed |La tâche s'est terminée avec succès. |
| Échec |Pour des [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md), le Runbook n’a pas pu être compilé. Pour des [runbooks de script PowerShell](automation-runbook-types.md), le runbook n’a pas pu démarrer ou la tâche a eu une exception. |
| Échec, en attente de ressources |La tâche a échoué, car elle a atteint la limite de [répartition de charge équilibrée](#fair-share) trois fois et a démarré à partir du même point de contrôle ou à partir du début du Runbook à chaque fois. |
| Mis en file d'attente. |La tâche attend que les ressources d'un travail Automation deviennent disponibles afin de pouvoir être démarrée. |
| Démarrage en cours |La tâche a été attribuée à un travail et le système la démarre. |
| Reprise |Le système reprend la tâche suspendue. |
| Exécution |La tâche est en cours d'exécution. |
| En cours d'exécution, en attente de ressources |La tâche a été déchargée, car elle a atteint la limite de [répartition de charge équilibrée](#fair-share) . Elle reprend bientôt depuis son dernier point de contrôle. |
| Arrêté |La tâche a été arrêtée par l'utilisateur avant qu'elle n'ait été terminée. |
| En cours d’arrêt |Le système arrête la tâche. |
| Interrompu |La tâche a été suspendue par l'utilisateur, le système ou une commande du Runbook. Si un runbook n’a pas de point de contrôle défini, il démarre à partir du début. S’il a un point de contrôle défini, il peut recommencer et reprendre à partir de son dernier point de contrôle. Le runbook est uniquement interrompu par le système en cas d’exception. Par défaut, ErrorActionPreference est définie sur **Continuer**, ce qui signifie que la tâche se poursuit en cas d'erreur. Si cette préférence est définie sur **Arrêter**, la tâche s'interrompt en cas d'erreur. S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement. |
| Suspension |Le système tente de suspendre la tâche à la demande de l’utilisateur. Le Runbook doit atteindre son prochain point de contrôle avant de pouvoir être suspendu. S'il a déjà passé le dernier point de contrôle, il se termine avant d'être suspendu. S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Affichage de l’état du travail à partir du portail Azure

Vous pouvez afficher un résumé de l’état de toutes les tâches du runbook ou explorer les détails d’une tâche spécifique du runbook dans le portail Azure. Vous pouvez aussi configurer l’intégration avec votre espace de travail Log Analytics pour transférer les flux de tâches et l’état des tâches de runbook. Pour plus d’informations sur l’intégration avec Log Analytics, voir [Transférer l’état d’un travail et des flux de travail d’Automation vers Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Résumé des tâches de Runbook Automation

À droite de votre compte Automation, vous pouvez observer un résumé de toutes les tâches de runbook sous la vignette **Statistiques des tâches**.

![Vignette Statistiques des tâches](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Cette vignette affiche un nombre et une représentation graphique de l’état de toutes les tâches exécutées.

Quand vous cliquez sur la vignette, la page **Travaux** s’affiche et présente une liste récapitulative de toutes les tâches exécutées. Cette page indique l’état, l’heure de début et l’heure de fin de chaque tâche.

![Page Tâches de compte Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Pour filtrer la liste des tâches, sélectionnez **Filtrer les tâches**, puis filtrez sur un runbook spécifique, un état de tâche ou, dans la liste déroulante, la plage de temps sur laquelle effectuer la recherche.

![Filtrer l’état des tâches](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Vous pouvez également afficher les détails d’un résumé des tâches d’un runbook spécifique en sélectionnant le runbook dans la page **Runbooks** de votre compte Automation, puis sélectionner la vignette **Tâches**. Dans la page **Tâches** qui s’ouvre, vous pouvez cliquer sur une tâche pour en afficher les détails et la sortie.

![Page Tâches de compte Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Résumé des tâches

Vous pouvez afficher la liste de toutes les tâches qui ont été créées pour un Runbook donné et leur état le plus récent. Vous pouvez filtrer cette liste par état de la tâche et par plage de dates de la dernière modification de la tâche. Pour afficher des informations détaillées et la sortie, cliquez sur le nom d’une tâche. La vue détaillée de la tâche inclut les valeurs des paramètres du Runbook qui ont été fournies à cette tâche.

Vous pouvez utiliser les étapes suivantes pour afficher les tâches d'un Runbook.

1. Dans le portail Azure, sélectionnez **Automation**, puis le nom d’un compte Automation.
2. À partir du hub, sélectionnez **Runbooks** puis, dans la page **Runbooks**, sélectionnez un runbook dans la liste.
3. Dans la page du runbook sélectionné, cliquez sur la vignette **Tâches**.
4. Cliquez sur une des tâches de la liste. Dans la page de détails de la tâche du runbook, vous voyez les détails et la sortie de la tâche.

## <a name="retrieving-job-status-using-windows-powershell"></a>Récupération de l'état d'une tâche à l'aide de Windows PowerShell

Vous pouvez utiliser [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) pour récupérer les tâches créées pour un Runbook et les détails d’une tâche particulière. Si vous démarrez un Runbook avec Windows PowerShell à l’aide de [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), il retourne la tâche résultante. Utilisez [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) pour obtenir la sortie d’une tâche.

Les exemples de commandes suivants récupèrent le dernier travail d’un exemple de runbook et affichent son état, les valeurs fournies pour les paramètres du runbook ainsi que la sortie du travail.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

L’exemple suivant récupère la sortie d’une tâche spécifique et retourne chaque enregistrement. Dans le cas de la survenue d’une exception pour un des enregistrements, l’exception est écrite au lieu de la valeur. Ce comportement est utile, car les exceptions peuvent fournir des informations supplémentaires, qui ne peuvent normalement pas être enregistrées au cours de la sortie.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Obtenir les détails du journal d’activité

Vous pouvez récupérer d’autres détails tels que le nom de la personne ou le compte qui a démarré le runbook, à partir du journal d’activité du compte Automation. L’exemple PowerShell suivant indique le dernier utilisateur à avoir exécuté le runbook en question :

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>répartition de charge équilibrée

Afin de partager les ressources entre tous les runbooks du cloud, Azure Automation décharge ou arrête temporairement les tâches dont l’exécution a commencé depuis plus de trois heures. Les tâches de [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) et de [runbooks Python](automation-runbook-types.md#python-runbooks) sont arrêtées, mais ne sont pas reprises, et leur état affiche Stopped (Arrêté).

Pour les tâches de longue durée, il est recommandé d’utiliser un [Runbook Worker hybride](automation-hrw-run-runbooks.md#job-behavior). Les Runbooks Workers hybrides ne sont pas limités par la répartition de charge équilibrée et n’ont pas de limitation en termes de durée d’exécution d’un runbook. Les autres [limites](../azure-subscription-service-limits.md#automation-limits) du travail s’appliquent à la fois aux bacs à sable Azure et aux Runbooks Workers hybrides. Les Runbooks Workers hybrides ne sont pas limités par la répartition de charge équilibrée de trois heures, mais les runbooks s’exécutant sur eux doivent néanmoins être développés pour prendre en charge les comportements de redémarrage après un problème inattendu avec l’infrastructure locale.

Une autre option consiste à optimiser le runbook en utilisant des runbooks enfants. Si votre runbook exécute une boucle via la même fonction sur plusieurs ressources, comme une opération de base de données sur diverses bases de données, vous pouvez déplacer cette fonction vers un [runbook enfant](automation-child-runbooks.md) et l’appeler à l’aide de l’applet de commande [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Chacun de ces runbooks enfants s’exécute en parallèle dans des processus distincts, diminuant ainsi le temps total d’exécution du runbook parent. Vous pouvez utiliser l’applet de commande [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) dans votre runbook pour vérifier l’état du travail de chaque enfant et déterminer si des opérations doivent être effectuées à la fin de l’exécution du runbook enfant.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook dans Azure Automation, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md)
