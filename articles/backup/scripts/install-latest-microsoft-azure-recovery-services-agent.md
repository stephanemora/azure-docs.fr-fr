---
title: Exemple de Script - Installer la dernière version de l’agent MARS sur des serveurs Windows locaux
description: Découvrez comment utiliser un script pour installer la dernière version de l’agent MARS sur vos serveurs Windows locaux dans un compte de stockage.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 1080149d33eebed160449865d58f422e6eba36b1
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560810"
---
# <a name="powershell-script-to-install-the-latest-mars-agent-on-an-on-premises-windows-server"></a>Script PowerShell pour installer la dernière version de l’agent MARS sur un serveur Windows local

Ce script vous permet d’installer la dernière version de l’agent MARS sur votre serveur Windows local.

## <a name="sample-script"></a>Exemple de script

```azurepowershell
<#

.SYNOPSIS
Sets MARS agent

.DESCRIPTION
Sets MARS agent

.ROLE
Administrators

#>
Set-StrictMode -Version 5.0
$ErrorActionPreference = "Stop"
Try {
    $agentPath = $env:TEMP + '\MARSAgentInstaller.exe'
    Invoke-WebRequest -Uri 'https://aka.ms/azurebackup_agent' -OutFile $agentPath
    & $agentPath /q | out-null

    $env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
    $azureBackupModuleName = 'MSOnlineBackup'
    $azureBackupModule = Get-Module -ListAvailable -Name $azureBackupModuleName
    if ($azureBackupModule) {
        $true
    }
    else {
        $false
    }
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="next-steps"></a>Étapes suivantes

[Découvrez-en plus](../backup-client-automation.md) sur l’utilisation de PowerShell pour déployer et gérer des sauvegardes locales à l’aide de l’agent MARS.