---
title: Exécution d'un Runbook dans Azure Automation
description: Décrit les détails du traitement d'un Runbook dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 38dd4d13aa45b69fc846ef9b6b2e1b56f56de573
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544753"
---
# <a name="runbook-execution-in-azure-automation"></a>Exécution d'un Runbook dans Azure Automation

Lorsque vous démarrez un Runbook dans Azure Automation, une tâche est créée. Une tâche est une instance d'exécution unique d'un Runbook. Un travail Azure Automation est assigné pour exécuter chaque tâche. Même si les travaux sont partagés par de nombreux comptes Azure, les tâches des différents comptes Automation sont isolées les unes des autres. Vous n’avez pas le contrôle du travail qui traite la requête de votre tâche. Un même runbook peut avoir beaucoup de tâches qui s’exécutent simultanément. L’environnement d’exécution pour les travaux du même compte Automation peut être réutilisé. Plus vous exécutez de travaux simultanément, plus ils peuvent être répartis vers le même bac à sable. Les travaux qui sont exécutés dans le même processus de bac à sable peuvent s’influencer mutuellement, par exemple avec le cmdlet `Disconnect-AzureRMAccount`. Lorsque ce cmdlet est exécuté, chaque travail de runbook est déconnecté dans le processus de bac à sable partagé. Lorsque vous affichez la liste des Runbooks du portail Azure, vous voyez l'état de toutes les tâches démarrées pour chaque Runbook. Vous pouvez afficher la liste des tâches de chaque runbook pour en assurer le suivi de l’état. Les journaux de travail sont stockés pendant 30 jours maximum. Pour obtenir une description des différents états des tâches, consultez [États des tâches](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Le diagramme suivant illustre le cycle de vie d’une tâche de runbook pour [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks), [runbooks graphiques](automation-runbook-types.md#graphical-runbooks) et [runbooks PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks).

![États des tâches - Workflow PowerShell](./media/automation-runbook-execution/job-statuses.png)

Vos tâches auront accès à vos ressources Azure en créant une connexion à votre abonnement Azure. Ils auront uniquement accès aux ressources de votre centre de données si ces ressources sont accessibles depuis le cloud public.

## <a name="where-to-run-your-runbooks"></a>Où exécuter vos runbooks

Les runbooks d'Azure Automation peuvent s'exécuter dans un bac à sable Azure ou dans un [Runbook Worker hybride](automation-hybrid-runbook-worker.md). Un bac à sable est un environnement Azure partagé qui peut être utilisé par plusieurs travaux. Les travaux qui utilisent le même bac à sable sont liés par les limitations de ressources du bac à sable. Workers hybrides peuvent exécuter des runbooks directement sur l’ordinateur qui héberge le rôle et sur les ressources dans l’environnement pour gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs assignés. La plupart des procédures opérationnelles peuvent facilement être exécuté dans les bacs à sable Azure. Dans certains cas, il est préférable de choisir un Runbook hybride plutôt qu'un bac à sable Azure pour exécuter votre runbook. Quelques exemples de scénarios sont fournis dans le tableau suivant :

|Tâche|Meilleur choix|Notes|
|---|---|---|
|Intégration à des ressources Azure|Bac à sable Azure|Hébergé dans Azure, l'authentification est plus simple. Si vous utilisez un Runbook Worker hybride sur une machine virtuelle Azure, vous pouvez utiliser des [identités managées pour les ressources Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Performances optimales pour gérer les ressources Azure|Bac à sable Azure|Script est exécuté dans le même environnement, qui à son tour possède moins de latence|
|Réduction des coûts d'exploitation|Bac à sable Azure|En l'absence de surcharge de calcul, aucune machine virtuelle n'est nécessaire|
|Script durable|Runbook Worker hybride|Les bacs à sable Azure sont [limités en termes de ressources](../azure-subscription-service-limits.md#automation-limits)|
|Interaction avec les services locaux|Runbook Worker hybride|Peut bénéficier d'un accès direct à la machine hôte|
|Logiciels et fichiers exécutables tiers requis|Runbook Worker hybride|Vous gérez le système d'exploitation et pouvez installer des logiciels|
|Surveillance d'un fichier ou d'un dossier avec un runbook|Runbook Worker hybride|Utilisez une [tâche Watcher](automation-watchers-tutorial.md) sur un Runbook Worker hybride|
|Script gourmand en ressources|Runbook Worker hybride| Les bacs à sable Azure sont [limités en termes de ressources](../azure-subscription-service-limits.md#automation-limits)|
|Utilisation de modules aux exigences spécifiques| Runbook Worker hybride|Voici quelques exemples :</br> **WinSCP** - dépendance à winscp.exe </br> **IISAdministration** - requiert l'activation d'IIS|
|Installation d'un module nécessitant un programme d'installation|Runbook Worker hybride|Modules pour le bac à sable doivent être qui peut être copiées|
|Utilisation de runbooks ou de modules nécessitant une version de .NET Framework autre que la version 4.7.2|Runbook Worker hybride|Les bacs à sable Automation disposent de .NET Framework 4.7.2, et aucune mise à niveau n'est possible|
|Scripts qui requièrent une élévation|Runbook Worker hybride|Les bacs à sable ne permettent pas d’élévation. Pour résoudre ce problème, utilisez un Runbook Worker hybride et vous pouvez désactiver l’UAC et utilisez `Invoke-Command` lorsque l’exécution de la commande qui nécessite une élévation|
|Scripts qui requièrent l’accès à WMI|Runbook Worker hybride|Travaux en cours d’exécution dans les bacs à sable dans le cloud [n’ont pas accès à WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Comportement des runbooks

Les runbooks s'exécutent selon la logique avec laquelle ils ont été définis. Si un runbook est interrompu, il redémarre au début. Compte tenu de ce comportement, les runbooks doivent être écrits de manière à pouvoir être redémarrés en cas de problèmes transitoires.

Tâches PowerShell démarrées à partir d’un Runbook a été exécuté dans Azure bac à sable ne peut-être pas s’exécuter en mode langage complet. Pour en savoir plus sur les modes de langage PowerShell, consultez [modes de langage PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Pour plus d’informations sur la façon d’interagir avec les travaux dans Azure Automation, consultez [récupération état du travail avec PowerShell](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>Création de ressources

Si votre script crée des ressources, vous devez commencer par vérifier qu'aucune ressource identique n'existe avant de tenter de la créer à nouveau. Un exemple de base est présenté ci-dessous :

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Scripts dépendants du temps

Une attention particulière doit être portée à l'élaboration des runbooks. Comme mentionné précédemment, les runbooks doivent être créés de manière à être robustes et à pouvoir gérer les erreurs transitoires qui peuvent entraîner leur redémarrage ou leur échec. Si un runbook échoue, elle est retentée. Si un runbook s’exécute normalement dans une contrainte de temps, une logique pour vérifier la durée d’exécution doit être implémentée dans le runbook pour vous assurer d’opérations telles que de démarrer, arrêter ou de monter en charge sont exécutés uniquement à des moments spécifiques.

### <a name="tracking-progress"></a>Suivi de la progression

Il est conseillé de créer des runbooks modulaires. En d'autres termes, il convient de structurer la logique du runbook afin de faciliter sa réutilisation et son redémarrage. Suivi de la progression dans un runbook est un bon moyen de vous assurer que la logique dans un runbook s’exécute correctement si vous rencontrez des problèmes. Vous pouvez également suivre la progression du runbook via des sources externes telles que des comptes de stockage, une base de données ou des fichiers partagés. En effectuant le suivi de l’état en externe, vous pouvez créer logique dans votre runbook pour vérifier l’état de la dernière action qu'a eu le runbook. Puis basée sur les résultats, soit ignorer ou continuer des tâches spécifiques dans le runbook.

### <a name="prevent-concurrent-jobs"></a>Prévention des travaux simultanés

Certains runbooks peuvent se comporter bizarrement lorsqu'ils exécutent plusieurs travaux en même temps. Dans ce cas, il est important d'implémenter une logique permettant de déterminer si le runbook exécute déjà un travail. Un exemple de base de la procédure à suivre est présenté ci-dessous :

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Utilisation de plusieurs abonnements

Lors de la création de runbooks qui gèrent plusieurs abonnements, votre runbook doit utiliser le [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) applet de commande pour vous assurer que votre contexte d’authentification n’est pas récupéré à partir d’un autre runbook qui peut-être être en cours d’exécution dans le même bac à sable. Vous devez ensuite utiliser le `-AzureRmContext` paramètre sur votre `AzureRM` applets de commande et le passer votre contexte approprié.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Gestion des exceptions

Lors de la création de scripts, il est important de pouvoir gérer des exceptions et échecs intermittents potentiels. Voici différentes méthodes pour gérer des exceptions ou des problèmes intermittents avec vos runbooks :

#### <a name="erroractionpreference"></a>$ErrorActionPreference

Le [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) variable de préférence détermine la façon dont PowerShell répond à une erreur sans fin d’exécution. Des erreurs avec fin d’exécution ne sont pas affectées par `$ErrorActionPreference`, ils arrête toujours. À l’aide de `$ErrorActionPreference`, une erreur normalement sans fin d’exécution de `PathNotFound` à partir de la `Get-ChildItem` applet de commande arrête l’exécution du runbook. L’exemple suivant illustre l’utilisation `$ErrorActionPreference`. La dernière `Write-Output` ligne ne sera jamais exécutée comme le script s’arrête.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) est utilisé dans les scripts PowerShell pour vous aider à gérer les erreurs avec fin d’exécution. À l’aide de Try Catch, vous pouvez intercepter des exceptions spécifiques ou des exceptions générales. L’instruction Catch doit être utilisée pour effectuer le suivi des erreurs ou pour tenter de gérer l’erreur. L’exemple suivant tente de télécharger un fichier qui n’existe pas. Il intercepte le `System.Net.WebException` exception, s’il y avait une autre exception la dernière valeur est retournée.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Lever](/powershell/module/microsoft.powershell.core/about/about_throw) peut être utilisé pour générer une erreur avec fin d’exécution. Cela peut être utile lorsque vous définissez votre propre logique dans un runbook. Si un certain critère est remplie qui doit s’arrêter le script, vous pouvez utiliser `throw` pour arrêter le script. L’exemple suivant illustre un paramètre de fonction requis à l’aide de l’ordinateur `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Utilisation de fichiers exécutables ou appel de processus

Les runbooks s’exécutent dans les bacs à sable Azure ne gèrent pas le processus appelants (par exemple, .exe ou subprocess.call). Il s’agit des bacs à sable Azure étant partagé des processus exécutés dans des conteneurs, qui peut-être pas accès à toutes les API sous-jacentes. Pour les scénarios nécessitant un logiciel tiers ou l'appel de sous-processus, il est recommandé d'exécuter le runbook sur un [Runbook Worker hybride](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Caractéristiques de l’appareil et l’application

Tâches de Runbook s’exécutent dans les bacs à sable Azure n’ont pas accès à des caractéristiques du périphérique ou une application. L’API courant utilisé pour interroger les métriques de performances sur Windows est WMI. Certaines de ces mesures courantes sont l’utilisation du processeur et mémoire. Toutefois, n’importe quel API est utilisée. Travaux en cours d’exécution dans le cloud ont accès à l’implémentation Microsoft de Web WBEM Based Enterprise Management (), qui est basé sur le modèle CIM (Common Information), qui sont les normes du secteur pour la définition des caractéristiques de l’appareil et l’application.

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
| Interrompu |La tâche a été suspendue par l'utilisateur, le système ou une commande du Runbook. Si un runbook n’a pas de point de contrôle défini, il démarre à partir du début. S’il a un point de contrôle défini, il peut recommencer et reprendre à partir de son dernier point de contrôle. Le runbook n'est interrompu par le système qu'en cas d'exception. Par défaut, ErrorActionPreference est définie sur **Continuer**, ce qui signifie que la tâche se poursuit en cas d'erreur. Si cette préférence est définie sur **Arrêter**, la tâche s'interrompt en cas d'erreur. S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement. |
| Suspension |Le système tente de suspendre la tâche à la demande de l’utilisateur. Le Runbook doit atteindre son prochain point de contrôle avant de pouvoir être suspendu. S'il a déjà passé le dernier point de contrôle, il se termine avant d'être suspendu. S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Affichage de l’état du travail à partir du portail Azure

Vous pouvez afficher un résumé de l’état de toutes les tâches du runbook ou explorer les détails d’une tâche spécifique du runbook dans le portail Azure. Vous pouvez également configurer une intégration à votre espace de travail Log Analytics pour transférer l'état et les flux de travaux du runbook. Pour plus d’informations sur l’intégration avec les journaux d’Azure Monitor, consultez [transférer l’état du travail et flux de travail d’Automation dans les journaux d’Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Résumé des tâches de Runbook Automation

À droite de votre compte Automation, vous pouvez voir un résumé de tous les travaux du runbook sous la vignette **Statistiques des travaux**.

![Vignette Statistiques des tâches](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Cette vignette affiche un nombre et une représentation graphique de l’état de toutes les tâches exécutées.

Lorsque vous cliquez sur la vignette, la page **Travaux** s'affiche avec un récapitulatif de tous les travaux exécutés. Cette page indique l’état, l’heure de début et l’heure de fin de chaque tâche.

![Page Tâches de compte Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Pour filtrer la liste des tâches, sélectionnez **Filtrer les tâches**, puis filtrez sur un runbook spécifique, un état de tâche ou, dans la liste déroulante, la plage de temps sur laquelle effectuer la recherche.

![Filtrer l’état des tâches](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Vous pouvez également afficher les détails d’un résumé des tâches d’un runbook spécifique en sélectionnant le runbook dans la page **Runbooks** de votre compte Automation, puis sélectionner la vignette **Tâches**. Sur la page **Travaux** qui s'ouvre, vous pouvez cliquer sur un travail pour en afficher les détails et la sortie.

![Page Tâches de compte Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Résumé des tâches

Vous pouvez afficher la liste de tous les travaux qui ont été créés pour un runbook donné, avec leur état le plus récent. Vous pouvez filtrer cette liste par état de la tâche et par plage de dates de la dernière modification de la tâche. Pour afficher des informations détaillées et une sortie, cliquez sur le nom d'un travail. La vue détaillée de la tâche inclut les valeurs des paramètres du Runbook qui ont été fournies à cette tâche.

Vous pouvez utiliser les étapes suivantes pour afficher les tâches d'un Runbook.

1. Dans le portail Azure, sélectionnez **Automation**, puis le nom d’un compte Automation.
2. À partir du hub, sélectionnez **Runbooks** puis, dans la page **Runbooks**, sélectionnez un runbook dans la liste.
3. Dans la page du runbook sélectionné, cliquez sur la vignette **Tâches**.
4. Cliquez sur un des travaux de la liste pour afficher les détails et la sortie de ce travail du runbook.

## <a name="retrieving-job-status-using-powershell"></a>Récupération de l’état du travail à l’aide de PowerShell

Vous pouvez utiliser [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) pour récupérer les tâches créées pour un Runbook et les détails d’une tâche particulière. Si vous démarrez un runbook PowerShell à l’aide [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), il retourne la tâche résultante. Utilisez [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) pour obtenir la sortie d’une tâche.

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
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    { 
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>répartition de charge équilibrée

Pour partager des ressources entre tous les runbooks dans le cloud, Azure Automation décharge temporairement ou arrête un travail qui a exécuté pendant plus de trois heures. Les tâches de [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) et de [runbooks Python](automation-runbook-types.md#python-runbooks) sont arrêtées, mais ne sont pas reprises, et leur état affiche Stopped (Arrêté).

Pour les travaux de longue durée, il est recommandé d'utiliser un [Runbook Worker hybride](automation-hrw-run-runbooks.md#job-behavior). Les Runbook Workers hybrides ne sont pas limités par la répartition de charge équilibrée et n'imposent aucune limitation en termes de durée d'exécution des runbooks. Les autres [limites](../azure-subscription-service-limits.md#automation-limits) du travail s’appliquent à la fois aux bacs à sable Azure et aux Runbooks Workers hybrides. Tandis que les Workers hybrides ne sont pas limités par la limite de répartition de 3 heures, les runbooks s’exécutent dessus doit être développé pour prendre en charge les comportements de redémarrage à partir des problèmes d’infrastructure local inattendue.

Une autre option consiste à optimiser le runbook en utilisant des runbooks enfants. Si votre runbook exécute une boucle via la même fonction sur plusieurs ressources, comme une opération de base de données sur diverses bases de données, vous pouvez déplacer cette fonction vers un [runbook enfant](automation-child-runbooks.md) et l'appeler à l'aide de la cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Chacun de ces runbooks enfants s’exécute en parallèle dans des processus distincts. Ce comportement réduit la quantité totale de temps pour l’exécution du runbook parent. Vous pouvez utiliser la [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) applet de commande dans votre runbook pour vérifier l’état du travail pour chaque enfant d’opérations qui effectuent une fois que le runbook enfant se termine.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook dans Azure Automation, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md)

