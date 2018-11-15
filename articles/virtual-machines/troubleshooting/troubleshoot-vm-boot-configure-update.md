---
title: Le démarrage de la machine virtuelle est bloqué sur « Préparation de Windows. N’éteignez pas l’ordinateur. » dans Azure | Microsoft Docs
description: Présentation des étapes de résolution du problème de démarrage de machine virtuelle bloqué sur « Préparation de Windows. N’éteignez pas l’ordinateur. »
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2bcdb2b458327a5e87dc36e4a5f50f0ac46bf96a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621025"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Le démarrage de la machine virtuelle est bloqué sur « Préparation de Windows. N’éteignez pas l’ordinateur. » dans Azure

Cet article vous aide à résoudre le problème rencontré lorsque votre Machine virtuelle est bloquée à l’étape « Préparation de Windows. N’éteignez pas l’ordinateur. » lors du démarrage.

## <a name="symptoms"></a>Symptômes

Lorsque vous utilisez les **Diagnostics de démarrage** pour obtenir la capture d’écran d’une machine virtuelle, vous découvrez que le système d’exploitation ne démarre pas entièrement. Par ailleurs, la machine virtuelle affiche le message **« Préparation de Windows. N’éteignez pas l’ordinateur. »** .

![Exemple de message](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exemple de message](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Cause :

Ce problème se produit généralement lorsque le serveur effectue le redémarrage final après un changement de configuration. Le changement de configuration peut résulter de mises à jour de Windows ou de modifications des rôles ou fonctionnalité du serveur. Pour Windows Update, si l’ampleur des mises à jour est importante, le système d’exploitation a besoin de plus de temps pour reconfigurer les modifications.

## <a name="back-up-the-os-disk"></a>Sauvegarder le disque du système d’exploitation

Avant d’essayer de résoudre le problème, sauvegardez le disque du système d’exploitation :

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Pour des machines virtuelles dotées d’un disque chiffré, vous devez commencer par déverrouiller les disques

Vérifiez si la machine virtuelle est chiffrée. Pour ce faire, procédez comme suit :

1. Sur le portail, ouvrez votre machine virtuelle, puis accédez aux disques.

2. Vous voyez un appel de colonne « Chiffrement » qui vous l’indiquera si le chiffrement est activé.

Si le disque du système d’exploitation est chiffré, déverrouillez-le. Pour ce faire, procédez comme suit :

1. Créez une machine virtuelle de récupération dans les mêmes groupe de ressources, compte de stockage et emplacement que la machine virtuelle affectée.

2. Dans le portail Azure, supprimez la machine virtuelle affectée en conservant le disque.

3. Exécutez PowerShell ISE en tant qu’administrateur.

4. Exécutez la cmdlet suivante pour obtenir le nom du secret.

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Une fois le nom du secret en votre possession, exécutez les commandes suivantes dans PowerShell :

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Convertissez la valeur codée en Base64 en octets, et écrivez la sortie dans un fichier. 

    > [!Note]
    > Si vous utilisez l’option de déverrouillage d’USB, le nom du fichier BEK doit correspondre au GUID BEK d’origine. Avant de passer aux étapes suivantes, vous devez également créer sur votre lecteur C un dossier nommé « BEK ».
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Une fois le fichier BEK créé sur votre ordinateur, copiez-le vers la machine virtuelle Site Recovery à laquelle le disque du système d’exploitation verrouillé est attaché. Exécutez la commande suivante en utilisant l’emplacement du fichier BEK :

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Facultatif** : dans certains scénarios il peut être nécessaire de déchiffrer également le disque avec cette commande.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Cela en considérant que le lecteur à chiffrer est identifié par la lettre F.

8. Si vous avez besoin de collecter des journaux, vous pouvez naviguer vers le chemin d’accès **LETTRE DE LECTEUR:\Windows\System32\winevt\Logs**.

9. Détachez le lecteur de la machine de récupération.

### <a name="create-a-snapshot"></a>Créer un instantané

Pour créer un instantané, procédez de la manière décrite dans [Effectuer la capture instantanée d’un disque](..\windows\snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Collecter un vidage mémoire du système d’exploitation

Suivez les étapes décrites dans la section [Collecter un vidage mémoire du système d’exploitation](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) pour recueillir un vidage mémoire du système d’exploitation quand la machine virtuelle est bloquée au niveau de la configuration.

## <a name="contact-microsoft-support"></a>Contact Microsoft support

Après avoir recueilli le fichier de vidage mémoire, contactez le [Support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour lui demander d’analyser la cause racine.


## <a name="rebuild-the-vm-using-powershell"></a>Régénérer la machine virtuelle à l’aide de PowerShell

Après avoir recueilli le fichier de vidage mémoire, procédez comme suit pour régénérer la machine virtuelle.

**Pour les disques non managés**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Pour les disques managés**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
