---
title: Démarrer les ordinateurs à l’aide de runbooks Automation dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment démarrer les ordinateurs virtuels dans un laboratoire dans Azure DevTest Labs à l’aide de runbooks Azure Automation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996660"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Démarrer les machines virtuelles dans un laboratoire dans l’ordre à l’aide de runbooks Azure Automation
Le [autostart](devtest-lab-set-lab-policy.md#set-autostart) fonctionnalité de DevTest Labs vous permet configurer des machines virtuelles pour démarrer automatiquement à une heure spécifiée. Toutefois, cette fonctionnalité ne prend pas en charge les machines pour démarrer dans un ordre spécifique. Il existe plusieurs scénarios où ce type d’automation peut être utile.  Un scénario est où une VM Jumpbox dans un laboratoire doit être démarrée en premier, avant les autres machines virtuelles, car le serveur de rebond est utilisé comme le point d’accès aux autres machines virtuelles.  Cet article vous montre comment configurer un compte Azure Automation avec un runbook PowerShell qui exécute un script. Le script utilise des balises sur les machines virtuelles dans le laboratoire pour vous permettent de contrôler l’ordre de démarrage sans avoir à modifier le script.

## <a name="setup"></a>Paramétrage
Dans cet exemple, les machines virtuelles dans le laboratoire doivent avoir la balise **StartupOrder** ajouté avec la valeur appropriée (0,1,2, etc..). Désigner n’importe quel ordinateur qui n’a pas besoin être démarré en tant que -1.

## <a name="create-an-azure-automation-account"></a>Créer un compte Azure Automation
Créer un compte Azure Automation en suivant les instructions dans [cet article](../automation/automation-create-standalone-account.md). Choisissez le **comptes d’identification** option lors de la création du compte. Une fois que le compte automation est créé, ouvrez le **Modules** page, puis sélectionnez **mise à jour les Modules Azure** sur la barre de menus. Les modules par défaut sont que plusieurs versions anciennes et sans la mise à jour le script peut ne pas fonctionnent.

## <a name="add-a-runbook"></a>Ajouter un runbook
Maintenant, pour ajouter un runbook dans le compte automation, sélectionnez **Runbooks** sur le menu de gauche. Sélectionnez **ajouter un runbook** sur le menu, puis suivez les instructions à [créer un runbook PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script PowerShell
Le script suivant prend le nom d’abonnement, le nom de laboratoire en tant que paramètres. Le flux du script est pour obtenir toutes les machines virtuelles dans le laboratoire, puis analyser les informations de balise pour créer une liste des noms de machine virtuelle et de leur ordre de démarrage. Le script Guide à travers les machines virtuelles dans l’ordre et démarre les machines virtuelles. S’il existe plusieurs machines virtuelles dans un numéro de commande spécifiques, ils sont démarrés de façon asynchrone à l’aide de travaux PowerShell. Pour ces machines virtuelles qui n’ont pas une balise, la valeur de démarrage définie pour être le dernier (10), il démarrera derniers, par défaut.  Si le laboratoire ne veut pas être démarrée automatiquement de la machine virtuelle, définissez la valeur de balise sur 11 et il sera ignoré.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Créer une planification
Pour que ce script à exécuter tous les jours, [créer une planification](../automation/shared-resources/schedules.md#creating-a-schedule) dans le compte automation. Une fois que la planification est créée, [liez-le au runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

Dans une situation à grande échelle où il existe plusieurs abonnements avec plusieurs laboratoires, stocker les informations de paramètre dans un fichier pour les laboratoires différents et transmettez le fichier au script plutôt que les paramètres individuels. Le script aurait besoin d’être modifiée, mais l’exécution de core serait la même. Bien que cet exemple utilise Azure Automation pour exécuter le script PowerShell, il existe des autres options, comme l’utilisation d’une tâche dans un pipeline de Build/mise en production.

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant pour en savoir plus sur Azure Automation : [Introduction à Azure Automation](../automation/automation-intro.md).
