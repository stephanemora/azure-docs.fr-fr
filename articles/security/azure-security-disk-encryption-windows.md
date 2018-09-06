---
title: Activer Azure Disk Encryption pour les machines virtuelles IaaS Windows | Microsoft Docs
description: Cet article fournit des instructions sur l’activation de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 8b3905c8-844f-4ec7-ad95-b386e9843053
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: 2d43c906fa717b036382a119efbaa2551fe50b1f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887887"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Activer Azure Disk Encryption pour les machines virtuelles IaaS Windows 

Il existe de nombreux scénarios permettant d’activer le chiffrement de disque et les étapes peuvent varier selon le scénario. Les sections suivantes décrivent ces scénarios de façon plus détaillée pour les machines virtuelles IaaS Windows. Avant de pouvoir utiliser le chiffrement de disque, les [prérequis pour Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) doivent être satisfaits. 

Prenez un [instantané](../virtual-machines/windows/snapshot-copy-managed-disk.md) et/ou faites une sauvegarde avant de chiffrer les disques. Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue lors du chiffrement. Les machines virtuelles avec des disques managés imposent une sauvegarde avant que le chiffrement soit effectué. Une fois la sauvegarde effectuée, vous pouvez utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques managés en spécifiant le paramètre -skipVmBackup. Pour plus d’informations sur la façon de sauvegarder et de restaurer des machines virtuelles chiffrées, consultez l’article [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> Azure Disk Encryption a besoin que Key Vault et les machines virtuelles se trouvent dans la même région. Créez et utilisez un coffre de clés situé dans la même région que la machine virtuelle à chiffrer. 


## <a name="bkmk_RunningWinVM"></a> Activer le chiffrement sur des machines virtuelles IaaS Windows existantes ou en cours d’exécution
Dans ce scénario, vous pouvez activer le chiffrement avec un modèle, avec des applets de commande PowerShell ou avec des commandes CLI. Les sections suivantes expliquent en détail comment activer Azure Disk Encryption. 

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois la sauvegarde effectuée, il est possible d’utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. La commande Set-AzureRmVMDiskEncryptionExtension échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Activer le chiffrement sur des machines virtuelles existantes ou en cours d’exécution avec Azure PowerShell 
Utilisez la commande [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure. 

-  **Chiffrer une machine virtuelle en cours d’exécution :** le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzureRmVMDiskEncryptionExtension. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. Remplacez MySecureRg, MySecureVM et MySecureVault par vos valeurs.

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK :** 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Vérifier que les disques sont chiffrés :** pour vérifier l’état du chiffrement d’une machine virtuelle IaaS, utilisez l’applet de commande [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Désactiver le chiffrement de disque :** pour désactiver le chiffrement, utilisez l’applet de commande [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a> Activer le chiffrement sur des machines virtuelles existantes ou en cours d’exécution avec Azure CLI
Utilisez la commande [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure.

-  **Chiffrer une machine virtuelle en cours d’exécution :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés] </br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Vérifier que les disques sont chiffrés :** pour vérifier l’état du chiffrement d’une machine virtuelle IaaS, utilisez la commande [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Désactiver le chiffrement :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois la sauvegarde effectuée, il est possible d’utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. Cette commande échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 

### <a name="bkmk_RunningWinVMwRM"> </a>Utilisation du modèle Resource Manager
Vous pouvez activer le chiffrement de disque sur des machines virtuelles Windows IaaS existantes ou en cours d’exécution dans Azure en utilisant le [modèle Resource Manager pour chiffrer une machine virtuelle Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déployer sur Azure**.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement, les paramètres, les conditions juridiques et le contrat. Cliquez sur **Acheter** pour activer le chiffrement sur la machine virtuelle IaaS existante ou en cours d’exécution.

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour les machines virtuelles existantes ou en cours d’exécution :

| Paramètre | Description |
| --- | --- |
| vmName | Nom de la machine virtuelle d’exécution de l’opération de chiffrement. |
| keyVaultName | Nom du coffre de clés dans lequel la clé BitLocker doit être téléchargée. Vous pouvez l’obtenir en utilisant l’applet de commande `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` ou la commande CLI Azure « az keyvault list --resource-group "MySecureGroup" |Convertfrom-JSON »|
| keyVaultResourceGroup | Nom du groupe de ressources qui contient le coffre de clés|
|  keyEncryptionKeyURL | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Ce paramètre est facultatif si vous sélectionnez **nokek** dans la liste déroulante UseExistingKek. Si vous sélectionnez **kek** dans la liste déroulante UseExistingKek, vous devez entrer la valeur _keyEncryptionKeyURL_. |
| volumeType | Type de volume sur lequel l’opération de chiffrement est effectuée. Les valeurs valides sont _Système d’exploitation_, _Données_ et _Tous_. 
| forceUpdateTag | Passez à une valeur unique comme un GUID chaque fois que l’opération doit être exécutée de force. |
| resizeOSDisk | Si la partition du système d’exploitation doit être redimensionnée pour occuper tout le disque dur virtuel du système d’exploitation avant de fractionner le volume système. |
| location | Emplacement pour toutes les ressources. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Chiffrer le groupe de machines virtuelles identiques
Les [groupes identiques de machines virtuelles Azure](../virtual-machine-scale-sets/overview.md) vous permettent de créer et de gérer un groupe de machines virtuelles identiques et disposant d’une charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini. Utilisez la CLI ou Azure PowerShell pour chiffrer un groupe de machines virtuelles identiques.

###  <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Chiffrer des groupes de machines virtuelles identiques à l’aide d’Azure PowerShell
Utilisez la commande cmdlet [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) pour activer le chiffrement sur un groupe de machines virtuelles identiques Windows.

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution** :
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **Obtenir l’état de chiffrement d’un groupe de machines virtuelles identiques :** Utiliser la cmdlet [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption).
    
    ```azurepowershell-interactive
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Désactiver le chiffrement sur un groupe de machines virtuelles identiques :** Utiliser la cmdlet [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption). 

    ```azurepowershell-interactive
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```
###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Chiffrer des groupes de machines virtuelles identiques avec Azure CLI
Utilisez l’[activation de chiffrage az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-enable) pour activer le chiffrement sur un groupe de machines virtuelles identiques Windows. Si vous définissez la stratégie de mise à niveau du groupe identique sur mode manuel, démarrez le chiffrement avec [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). 

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution avec la KEK pour inclure la clé dans un wrapper**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **Désactiver l’état de chiffrement d’un groupe de machines virtuelles identiques :** Utiliser [Affichage du chiffrement az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **Désactiver l’état de chiffrement d’un groupe de machines virtuelles identiques :** Utiliser [Affichage du chiffrement az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

## <a name="bkmk_VHDpre"> </a> Nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels et de clés de chiffrement chiffrés par le client
Dans ce scénario, vous pouvez activer le chiffrement avec des applets de commande PowerShell ou avec des commandes CLI. 

Utilisez les instructions de l’annexe pour la préparer d’images préchiffrées qui peuvent être utilisées dans Azure. Une fois l’image créée, vous pouvez suivre la procédure décrite dans la section suivante pour créer une machine virtuelle Azure chiffrée.

* [Préparer un disque dur virtuel Windows déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Préparer un disque dur virtuel Linux déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois la sauvegarde effectuée, il est possible d’utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. La commande Set-AzureRmVMDiskEncryptionExtension échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 


### <a name="bkmk_VHDprePSH"> </a> Chiffrer des machines virtuelles avec des disques durs virtuels préchiffrés avec Azure PowerShell
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré avec l’applet de commande PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). L’exemple ci-dessous vous montre certains paramètres courants. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Activer le chiffrement sur un disque de données nouvellement ajouté
Vous pouvez [ajouter un nouveau disque à une machine virtuelle Windows avec PowerShell](../virtual-machines/windows/attach-disk-ps.md) ou via le [portail Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure PowerShell
 Quand vous utilisez PowerShell pour chiffrer un nouveau disque de machines virtuelles Windows, vous devez spécifier une nouvelle version de la séquence. La version de la séquence doit être unique. Le script ci-dessous génère un GUID pour la version de la séquence. Dans certains cas, un disque de données nouvellement ajouté peut être chiffré automatiquement par l’extension Azure Disk Encryption. Si cela se produit, nous recommandons de réexécuter l’applet de commande Set-AzureRmVmDiskEncryptionExtension avec la nouvelle version de la séquence.
 

-  **Chiffrer une machine virtuelle en cours d’exécution :** le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzureRmVMDiskEncryptionExtension. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. Remplacez MySecureRg, MySecureVM et MySecureVault par vos valeurs. Le paramètre -VolumeType est défini pour les disques de données et non pas pour le disque du système d’exploitation. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId  –SequenceVersion $sequenceVersion;
    ```
- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK :** Il peut être nécessaire d’ajouter le paramètre -VolumeType si vous chiffrez des disques de données et non le disque du système d’exploitation. 

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure CLI
 La commande Azure CLI vous propose automatiquement une nouvelle version de la séquence quand vous exécutez la commande pour activer le chiffrement. 
-  **Chiffrer une machine virtuelle en cours d’exécution :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault"--key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```


## <a name="disable-encryption"></a>Désactiver le chiffrement
Vous pouvez désactiver le chiffrement avec Azure PowerShell, Azure CLI ou un modèle Resource Manager. 

- **Désactiver le chiffrement de disque avec Azure PowerShell :** pour désactiver le chiffrement, utilisez l’applet de commande [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

- **Désactiver le chiffrement avec Azure CLI :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Désactiver le chiffrement avec un modèle Resource Manager :** 

    1. Cliquez sur **Déployer sur Azure** dans le modèle [Désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement, la machine virtuelle, le type de volume, les conditions juridiques et le contrat.
    3.  Cliquez sur **Acheter** pour désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour Linux](azure-security-disk-encryption-linux.md)
