---
title: Exécution d'un Runbook dans Azure Automation
description: Décrit les détails du traitement d'un Runbook dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c8968eb72b29b004d94e25433da65d3262287147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367140"
---
# <a name="runbook-execution-in-azure-automation"></a>Exécution d'un Runbook dans Azure Automation

Les runbooks s’exécutent selon la logique avec laquelle ils ont été définis. Si un runbook est interrompu, il redémarre au début. Ce comportement vous contraint d’écrire des runbooks qui prennent en charge le redémarrage si des problèmes temporaires se produisent.

Le fait de démarrer un runbook dans Azure Automation a pour effet de créer une tâche. Une tâche est une instance d’exécution unique du runbook. Chaque tâche peut accéder à des ressources Azure en établissant une connexion à votre abonnement Azure. La tâche n’a accès aux ressources de votre centre de données que si ces ressources sont accessibles à partir du cloud public.

Azure Automation charge un Worker d’exécuter chaque tâche pendant l’exécution du runbook. Même si les travaux sont partagés par de nombreux comptes Azure, les tâches des différents comptes Automation sont isolées les unes des autres. Vous ne pouvez pas décider du Worker qui traite la requête de votre tâche.

Quand vous affichez la liste des runbooks sur le portail Azure, elle indique l’état de chaque tâche qui a été démarrée pour chaque runbook. Azure Automation stocke les journaux de tâches pendant une durée maximale de 30 jours. 

Le diagramme suivant illustre le cycle de vie d’une tâche de runbook pour les [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks), les [runbooks graphiques](automation-runbook-types.md#graphical-runbooks) et les [runbooks de workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![États des tâches - Workflow PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Où exécuter vos runbooks

Les runbooks d’Azure Automation peuvent s’exécuter dans un bac à sable Azure ou dans un [runbook Worker hybride](automation-hybrid-runbook-worker.md). La plupart des runbooks peuvent être facilement exécutés dans un bac à sable Azure, environnement partagé que plusieurs tâches peuvent utiliser. Les travaux qui utilisent le même bac à sable sont liés par les limitations de ressources du bac à sable.

Vous pouvez utiliser un runbook Worker hybride pour exécuter des runbooks directement sur l’ordinateur qui héberge le rôle et avec les ressources disponibles dans l’environnement. Azure Automation stocke et gère les runbooks et les remet à un ou plusieurs ordinateurs assignés.

Le tableau suivant liste certaines tâches d’exécution de runbook avec l’environnement d’exécution recommandé indiqué pour chacune d’elles.

|Tâche|Meilleur choix|Notes|
|---|---|---|
|Intégration à des ressources Azure|Bac à sable Azure|Hébergé dans Azure, l’authentification est plus simple. Si vous utilisez un runbook Worker hybride sur une machine virtuelle Azure, vous pouvez utiliser des [identités managées pour les ressources Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Obtention de performances optimales pour gérer les ressources Azure|Bac à sable Azure|Le script est exécuté dans le même environnement, dont la latence est moindre.|
|Réduction des coûts d'exploitation|Bac à sable Azure|Il n’y a pas de surcharge de calcul ni de besoin d’une machine virtuelle.|
|Exécution d’un script de longue durée|Runbook Worker hybride|Les bacs à sable Azure sont [limités en ressources](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interaction avec les services locaux|Runbook Worker hybride|Peut bénéficier d’un accès direct à la machine hôte.|
|Imposer des logiciels et des fichiers exécutables tiers|Runbook Worker hybride|Vous gérez le système d’exploitation et pouvez installer des logiciels.|
|Surveillance d'un fichier ou d'un dossier avec un runbook|Runbook Worker hybride|Utilisez une [tâche Watcher](automation-watchers-tutorial.md) sur un runbook Worker hybride.|
|Exécution d’un script gourmand en ressources|Runbook Worker hybride| Les bacs à sable Azure sont [limités en ressources](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Utilisation de modules ayant des exigences spécifiques| Runbook Worker hybride|Quelques exemples :</br> WinSCP – dépend de winscp.exe </br> IISAdministration – dépend de l’activation d’IIS.|
|Installation d’un module avec un programme d’installation|Runbook Worker hybride|Les modules pour bac à sable doivent prendre en charge la copie.|
|Utilisation de runbooks ou de modules nécessitant une version de .NET Framework différente de la version 4.7.2|Runbook Worker hybride|Les bacs à sable Automation disposent de .NET Framework 4.7.2, et aucune mise à niveau n’est possible.|
|Exécution de scripts qui nécessitent une élévation|Runbook Worker hybride|Les bacs à sable ne permettent pas l’élévation. Avec un runbook Worker hybride, vous pouvez désactiver le Contrôle de compte d’utilisateur (UAC) et utiliser **Invoke-Command** au moment d’exécuter la commande qui nécessite une élévation.|
|Exécution de scripts qui nécessitent un accès à WMI|Runbook Worker hybride|Les tâches s’exécutant dans les bacs à sable du cloud n’ont pas accès à WMI. |

## <a name="runbook-behavior"></a>Comportement des runbooks

### <a name="creating-resources"></a>Création de ressources

Si votre runbook crée une ressource, le script doit vérifier qu’elle n’existe pas déjà avant d’essayer de la créer. Voici un exemple simple.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Prise en charge des scripts dépendants de l’heure

Vos runbooks doivent être robustes et capables de gérer les erreurs temporaires qui peuvent entraîner leur redémarrage ou une défaillance. Si un runbook rencontre une défaillance, Azure Automation effectue un nouvel essai.

Si votre runbook s’exécute normalement dans les limites d’une contrainte de temps, faites en sorte que le script implémente la logique pour vérifier la durée d’exécution. Cette vérification contrôle la bonne exécution de certaines opérations comme le démarrage, l’arrêt ou le scale-out, uniquement à des moment précis.

> [!NOTE]
> L’heure locale du processus de bac à sable Azure est définie sur le temps universel coordonné (UTC). Les calculs de date et d’heure dans vos runbooks doivent prendre cet élément en considération.

### <a name="tracking-progress"></a>Suivi de la progression

Il est recommandé de créer des runbooks de nature modulaire en structurant leur logique de telle sorte qu’elle puisse être réutilisée et redémarrée facilement. Le suivi de la progression d’un runbook offre un bon moyen de vérifier que la logique du runbook s’exécute correctement en cas de problème. Il est possible de suivre la progression d’un runbook en utilisant une source externe, notamment un compte de stockage, une base de données ou des fichiers partagés. Vous pouvez créer une logique dans votre runbook qui vérifie dans un premier temps l’état de la dernière action effectuée. Ensuite, selon le résultat de la vérification, la logique peut ignorer ou poursuivre certaines tâches du runbook.

### <a name="preventing-concurrent-jobs"></a>Prévention des tâches simultanées

Certains runbooks peuvent se comporter bizarrement quand ils exécutent plusieurs tâches en même temps. Dans ce cas, il est important qu’un runbook implémente une logique qui puisse déterminer si une tâche est déjà en cours d’exécution. Voici un exemple simple.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
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

Pour permettre à votre runbook de gérer plusieurs abonnements, il doit utiliser l’applet de commande [Disable-AzureRmContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) pour éviter que le contexte d’authentification soit récupéré à partir d’un autre runbook s’exécutant dans le même bac à sable. Le runbook utilise aussi le paramètre `AzContext` des applets de commande du module Az, et lui passe le contexte approprié.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Gestion des exceptions

Cette section décrit des méthodes pour gérer les exceptions ou les problèmes intermittents qui se produisent dans vos runbooks.

#### <a name="erroractionpreference"></a>ErrorActionPreference

La variable [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) détermine la façon dont PowerShell répond à une erreur qui ne met pas fin à l’exécution. Les erreurs qui mettent fin à l’exécution se terminent toujours et ne sont pas affectées par *ErrorActionPreference*.

Quand le runbook utilise `ErrorActionPreference`, une erreur qui ne met normalement pas fin à l’exécution (comme **PathNotFound**) issue de l’applet de commande `Get-ChildItem` empêche le runbook d’aboutir. L’exemple suivant illustre l’utilisation de `ErrorActionPreference`. La commande finale `Write-Output` ne s’exécute jamais, puisque le script s’arrête.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) est utilisé dans les scripts PowerShell pour gérer les erreurs qui mettent fin à l’exécution. Le script peut utiliser ce mécanisme pour intercepter des exceptions spécifiques ou générales. L’instruction `catch` doit être utilisée pour suivre ou essayer de gérer les erreurs. L’exemple suivant essaie de télécharger un fichier qui n’existe pas. Il intercepte l’exception `System.Net.WebException` et retourne la dernière valeur pour toute autre exception.

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

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) peut être utilisée pour générer une erreur avec fin d’exécution. Ce mécanisme peut vous être utile au moment de définir votre propre logique dans un runbook. Si le script remplit un critère qui doit l’arrêter, il peut utiliser l’instruction `throw` pour s’arrêter. L’exemple suivant utilise cette instruction pour afficher un paramètre de fonction obligatoire.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Utilisation de fichiers exécutables ou appel de processus

Les runbooks qui s’exécutent dans les bacs à sable Azure ne prennent pas en charge les processus d’appel, comme les exécutables (fichiers **.exe**) ou les sous-processus.  Cela s’explique par le fait qu’un bac à sable Azure est un processus partagé qui s’exécute dans un conteneur qui n’a peut-être pas accès à toutes les API sous-jacentes. Pour les scénarios qui exigent un logiciel tiers ou des appels à des sous-processus, il est recommandé d’exécuter le runbook sur un [runbook Worker hybride](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Accès aux caractéristiques des appareils et des applications

Les tâches de runbook qui s’exécutent dans les bacs à sable Azure n’ont pas accès aux caractéristiques des appareils ou des applications. Pour interroger les métriques de performances sur Windows, notamment celles, courantes, qui portent sur l’utilisation de la mémoire et du processeur, L’API la plus utilisée est WMI. Cependant, quelle que soit l’API utilisée, les tâches qui s’exécutent dans le cloud n’ont pas accès à l’implémentation Microsoft de WBEM (Web-Based Enterprise Management). Cette plateforme est basée sur CIM (Common Information Model), qui fait office de standard sectoriel pour la définition des caractéristiques des appareils et des applications.

## <a name="handling-errors"></a>Gestion des erreurs

Vos runbooks doivent être capables de gérer les erreurs. Les erreurs de PowerShell sont de deux types : celles qui mettent fin à l’exécution et celles qui ne mettent pas fin à l’exécution. Quand elles se produisent, les erreurs qui mettent fin à l’exécution arrêtent l’exécution d’un runbook. Le runbook s’arrête et l’état de la tâche devient Échec.

Les erreurs qui ne mettent pas fin à l’exécution n’empêchent pas la poursuite d’un script. Par exemple, un runbook qui utilise l’applet de commande `Get-ChildItem` avec un chemin qui n’existe pas génère une erreur qui ne met pas fin à l’exécution. PowerShell détecte que le chemin n’existe pas, il génère une erreur et il passe au dossier suivant. Dans ce cas, l’erreur n’affecte pas l’état Échec à la tâche du runbook, laquelle peut même arriver à son terme. Pour forcer l’arrêt d’un runbook après une erreur sans fin d’exécution, utilisez `-ErrorAction Stop` avec l’applet de commande.

## <a name="handling-jobs"></a>Gestion des tâches

Vous pouvez réutiliser l’environnement d’exécution pour les tâches du même compte Automation. Un même runbook peut avoir beaucoup de tâches qui s’exécutent simultanément. Plus vous exécutez de travaux simultanément, plus ils peuvent être répartis vers le même bac à sable.

Les tâches qui s’exécutent dans le même processus de bac à sable peuvent s’influencer mutuellement. C’est le cas, par exemple, lorsque vous exécutez l’applet de commande `Disconnect-AzAccount`. Dans ce cas, cette applet de commande déconnecte chaque tâche du runbook dans le processus de bac à sable partagé.

Les tâches PowerShell démarrées à partir d’un runbook qui s’exécute dans un bac à sable Azure peut ne pas s’exécuter en mode langage complet. Pour en savoir plus sur les modes de langage PowerShell, consultez [Modes de langage PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Pour plus d’informations sur l’interaction avec les tâches dans Azure Automation, consultez [Récupération de l’état des tâches avec PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>États des tâches

Le tableau suivant décrit les différents états possibles d’une tâche.

| Statut | Description |
|:--- |:--- |
| Completed |La tâche s'est terminée avec succès. |
| Échec |Un runbook graphique ou un graphique de workflow PowerShell n’a pas pu être compilé. Un runbook de script PowerShell n’a pas pu démarrer ou la tâche a rencontré une exception. Consultez [Types de runbooks Azure Automation](automation-runbook-types.md).|
| Échec, en attente de ressources |La tâche a échoué, car elle a atteint la limite de [répartition de charge équilibrée](#fair-share) trois fois et a démarré à partir du même point de contrôle ou à partir du début du Runbook à chaque fois. |
| Mis en file d'attente. |La tâche attend que les ressources d’un Worker Automation deviennent disponibles pour pouvoir démarrer. |
| Démarrage en cours |La tâche a été attribuée à un travail et le système la démarre. |
| Reprise |Le système reprend la tâche suspendue. |
| Exécution en cours |La tâche est en cours d'exécution. |
| En cours d'exécution, en attente de ressources |La tâche a été déchargée, car elle a atteint la limite de répartition de charge équilibrée. Elle va bientôt reprendre depuis son dernier point de contrôle. |
| Arrêté |La tâche a été arrêtée par l'utilisateur avant qu'elle n'ait été terminée. |
| En cours d’arrêt |Le système arrête la tâche. |
| Interrompu |S’applique aux [runbooks graphiques et aux runbooks de workflow PowerShell](automation-runbook-types.md) uniquement. La tâche a été suspendue par l'utilisateur, le système ou une commande du Runbook. Si un runbook n’a pas de point de contrôle défini, il démarre à partir du début. S’il a un point de contrôle défini, il peut recommencer et reprendre à partir de son dernier point de contrôle. Le système suspend uniquement le runbook quand une exception se produit. Par défaut, la variable `ErrorActionPreference` est définie sur Continue, ce qui indique que la tâche continue de s’exécuter en cas d’erreur. Si la variable de préférence est définie sur Stop, la tâche est suspendue en cas d’erreur.  |
| Suspension |S’applique aux [runbooks graphiques et aux runbooks de workflow PowerShell](automation-runbook-types.md) uniquement. Le système tente de suspendre la tâche à la demande de l’utilisateur. Le Runbook doit atteindre son prochain point de contrôle avant de pouvoir être suspendu. S’il a déjà passé son dernier point de contrôle, il se termine avant d’être suspendu. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Affichage de l’état du travail à partir du portail Azure

Vous pouvez afficher un résumé de l’état de toutes les tâches du runbook ou explorer les détails d’une tâche spécifique du runbook dans le portail Azure. Vous pouvez également configurer une intégration à votre espace de travail Log Analytics pour transférer l’état et les flux de travaux du runbook. Pour plus d’informations sur l’intégration avec les journaux d’activité Azure Monitor, consultez [Transférer l’état d’un travail et des flux de travail d’Automation vers les journaux d’activité Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

À droite de votre compte Automation sélectionné, vous pouvez voir un résumé de toutes les tâches du runbook sous la vignette **Statistiques des tâches**.

![Vignette Statistiques des tâches](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Cette vignette affiche un nombre et une représentation graphique de l’état de chaque tâche exécutée.

Lorsque vous cliquez sur la vignette, la page Tâches s’affiche avec un récapitulatif de toutes les tâches exécutées. Cette page indique l’état, le nom du runbook, l’heure de début et l’heure de fin de chaque tâche.

![Page Tâches de compte Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Vous pouvez filtrer la liste des tâches en sélectionnant **Filtrer les tâches**. Filtrez en fonction d’un runbook spécifique, d’un état de tâche ou d’un choix dans la liste déroulante, puis indiquez un intervalle de temps pour la recherche.

![Filtrer en fonction de l’état des tâches](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Vous pouvez aussi afficher un résumé détaillé des tâches d’un runbook en sélectionnant le runbook dans la page Runbooks de votre compte Automation, puis en sélectionnant la vignette **Tâches**. Cette action affiche la page Tâches. De là, vous pouvez cliquer sur l’enregistrement de la tâche pour en afficher les détails et la sortie.

![Page Tâches de compte Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Affichage du résumé des tâches

Le résumé des tâches décrit ci-dessus vous permet d’examiner la liste de toutes les tâches qui ont été créées pour un runbook donné, avec leur état le plus récent. Pour afficher les informations détaillées et la sortie d’une tâche, cliquez sur son nom dans la liste. La vue détaillée de la tâche comprend les valeurs des paramètres du runbook qui ont été définies pour cette tâche.

Vous pouvez utiliser les étapes suivantes pour afficher les tâches d'un Runbook.

1. Dans le portail Azure, sélectionnez **Automation**, puis le nom d’un compte Automation.
2. À partir du hub, sélectionnez **Runbooks** sous **Automatisation des processus**.
3. Dans la page Runbooks, sélectionnez un runbook dans la liste.
3. Dans la page du runbook sélectionné, cliquez sur la vignette **Tâches**.
4. Cliquez sur l’une des tâches dans la liste et consultez-en les détails et la sortie dans la page de détails de la tâche du runbook.

### <a name="retrieving-job-status-using-powershell"></a>Récupération de l’état d’un travail avec Windows PowerShell

Utilisez l’applet de commande `Get-AzAutomationJob` pour récupérer les tâches créées pour un runbook, ainsi que les détails d’une tâche en particulier. Si vous démarrez un runbook avec PowerShell à l’aide de `Start-AzAutomationRunbook`, la tâche obtenue est retournée. Utilisez [AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) pour récupérer la sortie de la tâche.

Les exemples suivants obtiennent la dernière tâche d’un exemple de runbook et affichent son état, les valeurs définies pour les paramètres du runbook et la sortie de la tâche.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

L’exemple suivant récupère la sortie d’une tâche spécifique et retourne chaque enregistrement. Si une exception se produit pour l’un des enregistrements, le script écrit l’exception à la place de la valeur. Ce comportement est utile, car les exceptions peuvent fournir des informations supplémentaires qui ne sont pas nécessairement journalisées au moment de la sortie.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="getting-details-from-the-activity-log"></a>Obtention de détails du journal d’activité

Vous pouvez récupérer les détails d’un runbook, comme le nom de la personne ou le compte qui a démarré le runbook, à partir du journal d’activité du compte Automation. L’exemple PowerShell suivant indique le dernier utilisateur à avoir exécuté le runbook spécifié.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Partage de ressources entre runbooks

Pour répartir les ressources entre tous les runbooks du cloud, Azure Automation décharge ou arrête temporairement les travaux dont l’exécution dure depuis plus de trois heures. Les tâches des [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) et des [runbooks Python](automation-runbook-types.md#python-runbooks) sont arrêtées et non redémarrées, et leur état devient Arrêté.

Pour les tâches de longue durée, il est recommandé d’utiliser un runbook Worker hybride. Les Runbook Workers hybrides ne sont pas limités par la répartition de charge équilibrée et n'imposent aucune limitation en termes de durée d'exécution des runbooks. Les autres [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) du travail s’appliquent à la fois aux bacs à sable Azure et aux Runbooks Workers hybrides. Les runbooks Workers hybrides ne sont pas limités par la répartition de charge équilibrée de trois heures, mais vous devez développer des runbooks qui s’exécutent sur les Workers qui prennent en charge les redémarrages après des problèmes inattendus au niveau de l’infrastructure locale.

Une autre option consiste à optimiser un runbook en utilisant des runbooks enfants. Par exemple, il peut arriver que votre runbook exécute la même fonction en boucle sur plusieurs ressources, comme une opération de base de données sur diverses bases de données. Vous pouvez déplacer cette fonction dans un [runbook enfant](automation-child-runbooks.md) et faire en sorte que votre runbook l’appelle à l’aide de `Start-AzAutomationRunbook`. Les runbooks enfants s’exécutent en parallèle dans des processus distincts.

L’utilisation de runbooks enfants diminue le délai d’exécution total du runbook parent. Votre runbook peut utiliser l’applet de commande `Get-AzAutomationJob` pour vérifier l’état de la tâche d’un runbook enfant s’il a encore des opérations à effectuer une fois l’exécution du runbook enfant terminée.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les méthodes qui peuvent être utilisées pour démarrer un runbook dans Azure Automation, consultez [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md).
* Pour plus d’informations sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
