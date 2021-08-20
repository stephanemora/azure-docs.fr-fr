---
title: 'Exemple de script : Inscrire un serveur Windows ou un ordinateur client local auprès d’un coffre Recovery Services'
description: En savoir plus sur l’utilisation d’un script pour inscrire un serveur Windows ou un ordinateur client local auprès d’un coffre Recovery Services.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: a35d241ba86868c666916e284fb0700b59034eee
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560800"
---
# <a name="powershell-script-to-register-an-on-premises-windows-server-or-a-client-machine-with-recovery-services-vault"></a>Script PowerShell pour inscrire un serveur Windows ou un ordinateur client local auprès d’un coffre Recovery Services

Ce script vous permet d’inscrire un serveur Windows ou un ordinateur client local auprès d’un coffre Recovery Services. 

## <a name="sample-script"></a>Exemple de script

```azurepowershell
<#

.SYNOPSIS
Registers MARS agent

.DESCRIPTION
Registers MARS agent

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [String]
    $vaultcredPath,
    [Parameter(Mandatory = $true)]
    [String]
    $passphrase
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $date = Get-Date
    Start-OBRegistration -VaultCredentials $vaultcredPath -Confirm:$false
    $securePassphrase = ConvertTo-SecureString -String $passphrase -AsPlainText -Force
    Set-OBMachineSetting -EncryptionPassphrase $securePassphrase -SecurityPIN " "
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>Comment exécuter le script

1. Enregistrez le script ci-dessus sur votre ordinateur avec le nom de votre choix et l’extension .ps1.
1. Exécutez le script en indiquant les paramètres suivants :
   - – vaultcredPath : Chemin d’accès complet du fichier d’informations d’identification du coffre téléchargé.
   - – passphrase : Chaîne de texte brut convertie en chaîne sécurisée à l’aide de la cmdlet [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-7.1&preserve-view=true).

>[!Note]
>Vous devez également fournir le code confidentiel de sécurité généré à partir du portail Azure. Pour générer le code confidentiel, accédez à **Paramètres** -> **Propriétés** -> **Code confidentiel de sécurité** dans le panneau du coffre Recovery Services, puis sélectionnez **Générer**.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](../backup-client-automation.md) sur l’utilisation de PowerShell pour déployer et gérer des sauvegardes locales à l’aide de l’agent MARS.

