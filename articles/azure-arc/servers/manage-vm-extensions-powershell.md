---
title: Activer l’extension de machine virtuelle à l’aide d’Azure PowerShell
description: Cet article explique comment déployer des extensions de machine virtuelle sur des serveurs Azure Arc exécutés dans des environnements cloud hybrides avec Azure PowerShell.
ms.date: 10/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c759510c3ab81b15b65315015a16507dadf2658a
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074567"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Activer les extensions de machine virtuelle Azure à l’aide d’Azure PowerShell

Cet article explique comment déployer et désinstaller des extensions de machine virtuelle Azure, prises en charge par les serveurs Azure Arc, sur une machine hybride Linux ou Windows via Azure PowerShell.

> [!NOTE]
> Les serveurs avec Azure Arc ne prennent pas en charge le déploiement ni la gestion des extensions de machine virtuelle sur les machines virtuelles Azure. Pour les machines virtuelles Azure, consultez l’article [Vue d’ensemble de l’extension de machine virtuelle](../../virtual-machines/extensions/overview.md) suivant.

## <a name="prerequisites"></a>Prérequis

- Un ordinateur sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azure/).

Avant d’utiliser Azure PowerShell pour gérer les extensions de machine virtuelle sur votre serveur hybride géré par des serveurs Azure Arc, vous devez installer le module `Az.ConnectedMachine`. Ces opérations de gestion peuvent être effectuées à partir de votre station de travail, vous n’avez pas besoin de les exécuter sur le serveur Azure Arc.

Exécutez la commande suivante sur votre serveur Azure Arc :

`Install-Module -Name Az.ConnectedMachine`.

Une fois l’installation terminée, le message suivant est retourné :

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Activer l’extension

Pour activer une extension de machine virtuelle sur votre serveur Azure Arc, utilisez [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) avec les paramètres `-Name`, `-ResourceGroupName`, `-MachineName`, `-Location`, `-Publisher`, -`ExtensionType` et `-Settings`.

L’exemple suivant active l’extension de machine virtuelle Log Analytics sur un serveur Linux Azure Arc :

```powershell
$Setting = @{ "workspaceId" = "workspaceId" }
$protectedSetting = @{ "workspaceKey" = "workspaceKey" }
New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Pour activer l’extension de machine virtuelle Log Analytics sur un serveur Windows Azure Arc, modifiez la valeur du paramètre `-ExtensionType` en `"MicrosoftMonitoringAgent"` dans l’exemple précédent.

L'exemple suivant active l'extension de script personnalisé sur un serveur avec Azure Arc :

```powershell
$Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
New-AzConnectedMachineExtension -Name "custom" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

L’exemple suivant active l’extension Microsoft Antimalware sur un serveur Windows Azure Arc :

```powershell
$Setting = @{ "AntimalwareEnabled" = $true }
New-AzConnectedMachineExtension -Name "IaaSAntimalware" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Azure.Security" -Settings $Setting -ExtensionType "IaaSAntimalware"
```

### <a name="key-vault-vm-extension"></a>Extension de machine virtuelle Key Vault

> [!WARNING]
> Les clients PowerShell ajoutent souvent `\` à `"` dans settings.json, ce qui entraîne l’échec de akvvm_service avec l’erreur suivante : `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

L'exemple suivant active l'extension de machine virtuelle Key Vault sur un serveur avec Azure Arc :

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Lister les extensions installées

Pour obtenir la liste des extensions de machine virtuelle sur votre serveur Azure Arc, utilisez [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) avec les paramètres `-MachineName` et `-ResourceGroupName`.

Exemple :

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Supprimer une extension installée

Pour supprimer une extension de machine virtuelle installée sur votre serveur Azure Arc, utilisez [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) avec les paramètres `-Name`, `-MachineName` et `-ResourceGroupName`.

Par exemple, pour supprimer l’extension de machine virtuelle Log Analytics pour Linux, exécutez la commande suivante :

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez déployer, gérer et supprimer des extensions de machine virtuelle à l’aide d’[Azure CLI](manage-vm-extensions-cli.md), du [portail Azure](manage-vm-extensions-portal.md) ou de [modèles Azure Resource Manager](manage-vm-extensions-template.md).

- Des informations de dépannage sont accessibles dans le [guide Résoudre les problèmes liés aux extensions de machine virtuelle](troubleshoot-vm-extensions.md).
