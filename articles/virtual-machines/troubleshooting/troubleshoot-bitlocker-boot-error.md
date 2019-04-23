---
title: Résolution des erreurs de démarrage BitLocker dans une machine virtuelle Azure | Microsoft Docs
description: Découvrez comment résoudre les erreurs de démarrage BitLocker dans une machine virtuelle Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 51fc47a28cc40d286b5a268d4c42e3531f346c5e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796862"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Erreurs de démarrage BitLocker dans une machine virtuelle Azure

 Cet article décrit les erreurs BitLocker que vous pouvez rencontrer lorsque vous démarrez une machine virtuelle Windows dans Microsoft Azure.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="symptom"></a>Symptôme

 Une machine virtuelle Windows ne démarre pas. Lorsque vous regardez les captures d’écran de la fenêtre [Diagnostics de démarrage](../windows/boot-diagnostics.md), vous voyez l’un des messages d’erreur suivants :

- Connectez le pilote USB qui contient la clé BitLocker.

- Votre compte est verrouillé. Entrez la clé de récupération pour poursuivre (disposition du clavier : américain) Le nombre maximal de tentatives de connexion a été atteint. Votre PC a été verrouillé pour des raisons de sécurité. Pour récupérer la clé de récupération, accédez à https://windows.microsoft.com/recoverykeyfaq à partir d’un autre PC ou appareil mobile. Si vous en avez besoin, voici l’ID de la clé : XXXXXXX. Vous pouvez aussi réinitialiser votre PC.

- Entrez le mot de passe pour déverrouiller le lecteur [] Appuyez sur la touche Insertion pour afficher le mot de passe à mesure que vous le tapez.
- Entrez votre clé de récupération. Chargez votre clé de récupération à partir d’un périphérique USB.

## <a name="cause"></a>Cause :

Ce problème peut se produire lorsque la machine virtuelle ne peut pas localiser le fichier BEK pour déchiffrer le disque chiffré.

## <a name="solution"></a>Solution

Pour résoudre ce problème, arrêtez et libérez la machine virtuelle, et puis redémarrez-la. Cette opération force la machine virtuelle à récupérer le fichier BEK à partir d’Azure Key Vault, puis à la placer sur le disque chiffré. 

Si cette méthode ne résout pas le problème, effectuez les étapes suivantes pour restaurer le fichier BEK manuellement :

1. Prenez un instantané du disque système de la machine virtuelle affectée en guise de sauvegarde. Pour plus d’informations, consultez [Créer un instantané](../windows/snapshot-copy-managed-disk.md).
2. [Attachez le disque système à une machine virtuelle de récupération](troubleshoot-recovery-disks-portal-windows.md) chiffrée par BitLocker. Ceci est nécessaire pour exécuter la commande [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) qui est uniquement disponible sur la machine virtuelle chiffrée par BitLocker.

    Lorsque vous attachez un disque managé, vous pouvez recevoir un message d’erreur du type « Contient des paramètres de chiffrement et ne peut donc pas être utilisé comme disque de données ». Dans ce cas, exécutez le script suivant pour réessayer d’attacher le disque :

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Vous ne pouvez pas attacher un disque managé à une machine virtuelle qui a été restaurée à partir d’une image d’objet blob.

3. Une fois que le disque est attaché, établissez une connexion entre le Bureau à distance et la machine virtuelle de récupération, de manière à pouvoir exécuter des scripts Azure PowerShell. Vérifiez que vous avez installé [la dernière version d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur la machine virtuelle de récupération.

4. Ouvrez une session Azure PowerShell avec élévation de privilèges (Exécuter en tant qu’administrateur). Pour vous connecter à l’abonnement Azure, exécutez les commandes suivantes :

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Exécutez le script suivant pour vérifier le nom du fichier BEK :

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Voici un exemple de sortie : Recherchez le nom du fichier BEK pour le disque attaché. Ici, nous partons du principe que la lettre de lecteur du disque attaché est F, et que le fichier BEK est le suivant : EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Si vous voyez un volume en double, celui dont l’horodatage est le plus récent correspond au fichier BEK qui est actuellement utilisé par la machine virtuelle de récupération.

    Si le **Content Type** (Type de contenu) est **Wrapped BEK** (Clé BEK wrappée), accédez aux [scénarios impliquant des clés KEK (clés de chiffrement principales)](#key-encryption-key-scenario).

    Maintenant que vous connaissez le nom du fichier BEK pour le lecteur, vous devez créer le fichier secret-file-name.BEK pour déverrouiller le lecteur. 

6.  Téléchargez le fichier BEK sur le disque de récupération. L’exemple suivant enregistre le fichier BEK dans le dossier C:\BEK. Vérifiez que le chemin `C:\BEK\` existe avant d’exécuter les scripts.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Pour déverrouiller le disque attaché à l’aide du fichier BEK, exécutez la commande suivante :

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Dans cet exemple, le disque du système d’exploitation attaché est le lecteur F. Vérifiez que vous utilisez la bonne lettre de lecteur. 

    - Si le disque a pu être déverrouillé à l’aide de la clé BEK, nous considérerons que le problème BitLocker été résolu. 

    - Si l’utilisation de la clé BEK ne permet pas de déverrouiller le disque, vous pouvez choisir de suspendre la protection afin de désactiver temporairement BitLocker en exécutant la commande suivante :
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Si vous vous apprêtez à recréer la machine virtuelle à l’aide du disque dytem, vous devez déchiffrer entièrement le lecteur. Pour ce faire, exécutez la commande suivante :

        ```powershell
        manage-bde -off F:
        ```
8.  Détachez le disque de la machine virtuelle de récupération, puis rattachez-le à la machine virtuelle affectée en tant que disque système. Pour plus d’informations, consultez [Résoudre les problèmes d’une machine virtuelle Windows en connectant le disque du système d’exploitation à une machine virtuelle de récupération](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Scénario impliquant une clé de chiffrement principale

Dans un scénario impliquant une clé de chiffrement principale, effectuez les étapes suivantes :

1. Vérifiez que le compte d’utilisateur connecté nécessite l’autorisation « unwrapped » (non wrappée) dans les stratégies d’accès au coffre de clés sous **Utilisateur|Autorisations de clé|Opérations de chiffrement|Ne pas inclure la clé**.
2. Enregistrez les scripts suivants dans un fichier .PS1 :

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Définissez les paramètres. Le script va traiter le secret KEK pour créer la clé BEK, puis l’enregistrer dans un dossier local de la machine virtuelle de récupération.

4. La sortie suivante s’affiche au début de l’exécution du script :

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Une fois l’exécution du script terminée, vous voyez la sortie suivante :

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Pour déverrouiller le disque attaché à l’aide du fichier BEK, exécutez la commande suivante :

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Dans cet exemple, le disque du système d’exploitation attaché est le lecteur F. Vérifiez que vous utilisez la bonne lettre de lecteur. 

    - Si le disque a pu être déverrouillé à l’aide de la clé BEK, nous considérerons que le problème BitLocker été résolu. 

    - Si l’utilisation de la clé BEK ne permet pas de déverrouiller le disque, vous pouvez choisir de suspendre la protection afin de désactiver temporairement BitLocker en exécutant la commande suivante :
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Si vous vous apprêtez à recréer la machine virtuelle à l’aide du disque dytem, vous devez déchiffrer entièrement le lecteur. Pour ce faire, exécutez la commande suivante :

        ```powershell
        manage-bde -off F:
        ```

6. Détachez le disque de la machine virtuelle de récupération, puis rattachez-le à la machine virtuelle affectée en tant que disque système. Pour plus d’informations, consultez [Résoudre les problèmes d’une machine virtuelle Windows en connectant le disque du système d’exploitation à une machine virtuelle de récupération](troubleshoot-recovery-disks-windows.md).
