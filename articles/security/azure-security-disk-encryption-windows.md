---
title: Activer Azure Disk Encryption pour les machines virtuelles IaaS Windows
description: Cet article fournit des instructions sur l’activation de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: f17dc61f47dadf4c808467b2158cd9ef034e1ce9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277117"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Activer Azure Disk Encryption pour les machines virtuelles IaaS Windows

Il existe de nombreux scénarios permettant d’activer le chiffrement de disque et les étapes peuvent varier selon le scénario. Les sections suivantes décrivent ces scénarios de façon plus détaillée pour les machines virtuelles IaaS Windows. Avant de pouvoir utiliser le chiffrement de disque, les [prérequis pour Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) doivent être satisfaits. 

Prenez un [instantané](../virtual-machines/windows/snapshot-copy-managed-disk.md) et/ou faites une sauvegarde avant de chiffrer les disques. Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue lors du chiffrement. Les machines virtuelles avec des disques managés imposent une sauvegarde avant que le chiffrement soit effectué. Une fois qu’une sauvegarde est effectuée, vous pouvez utiliser l’applet de commande Set-AzVMDiskEncryptionExtension pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. Pour plus d’informations sur la façon de sauvegarder et de restaurer des machines virtuelles chiffrées, consultez l’article [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Si vous avez déjà utilisé [Azure Disk Encryption avec une application Azure AD](azure-security-disk-encryption-prerequisites-aad.md) pour chiffrer cette machine virtuelle, vous devrez continuer à utiliser cette option. Vous ne pouvez pas utiliser [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) sur cette machine virtuelle chiffrée car un tel scénario n’est pas pris en charge. Autrement dit, l’utilisation d’une autre application que l’application AAD pour cette machine virtuelle n’est pas encore prise en charge. 
> - Azure Disk Encryption a besoin que Key Vault et les machines virtuelles se trouvent dans la même région. Créez et utilisez un coffre de clés situé dans la même région que la machine virtuelle à chiffrer. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Activer le chiffrement sur des machines virtuelles IaaS Windows existantes ou en cours d’exécution
Dans ce scénario, vous pouvez activer le chiffrement avec un modèle, avec des applets de commande PowerShell ou avec des commandes CLI. Les sections suivantes expliquent en détail comment activer Azure Disk Encryption. Si vous avez besoin d’informations de schéma pour l’extension de machine virtuelle, consultez l’article [Azure Disk Encryption pour extension Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois qu’une sauvegarde est effectuée, l’applet de commande Set-AzVMDiskEncryptionExtension peut être utilisé pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. La commande Set-AzVMDiskEncryptionExtension échoue sur les machines virtuelles avec disque managé en fonction jusqu'à ce qu’une sauvegarde a été effectuée et que ce paramètre a été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Activer le chiffrement sur des machines virtuelles existantes ou en cours d’exécution avec Azure PowerShell 
Utilisez le [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) applet de commande pour activer le chiffrement sur une machine virtuelle IaaS dans Azure. 

-  **Chiffrer une machine virtuelle en cours d’exécution :** Le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzVMDiskEncryptionExtension. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. Remplacez MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM et MySecureVault par vos valeurs.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Chiffrer une machine virtuelle à l’aide de la KEK :** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Vérifier que les disques sont chiffrés :** Pour vérifier l’état de chiffrement d’une VM IaaS, utilisez le [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) applet de commande. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Désactiver le chiffrement de disque :** Pour désactiver le chiffrement, utilisez la [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) applet de commande. La désactivation du chiffrement de disque de données sur la machine virtuelle Windows lorsque les disques du système d’exploitation et de données ont été chiffrés ne fonctionne pas comme prévu. Désactivez plutôt le chiffrement sur tous les disques.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a> Activer le chiffrement sur des machines virtuelles existantes ou en cours d’exécution avec Azure CLI
Utilisez la commande [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure.

- **Chiffrer une machine virtuelle en cours d’exécution :**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Chiffrer une machine virtuelle à l’aide de la KEK :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés] </br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Vérifier que les disques sont chiffrés :** Pour vérifier l’état de chiffrement d’une VM IaaS, utilisez le [show de chiffrement de machine virtuelle az](/cli/azure/vm/encryption#az-vm-encryption-show) commande. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Désactiver le chiffrement :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). La désactivation du chiffrement de disque de données sur la machine virtuelle Windows lorsque les disques du système d’exploitation et de données ont été chiffrés ne fonctionne pas comme prévu. Désactivez plutôt le chiffrement sur tous les disques.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois qu’une sauvegarde est effectuée, l’applet de commande Set-AzVMDiskEncryptionExtension peut être utilisé pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. Cette commande échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
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
| keyVaultName | Nom du coffre de clés dans lequel la clé BitLocker doit être téléchargée. Vous pouvez l’obtenir à l’aide de l’applet de commande `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou la commande CLI Azure `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nom du groupe de ressources qui contient le coffre de clés|
|  keyEncryptionKeyURL | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Ce paramètre est facultatif si vous sélectionnez **nokek** dans la liste déroulante UseExistingKek. Si vous sélectionnez **kek** dans la liste déroulante UseExistingKek, vous devez entrer la valeur _keyEncryptionKeyURL_. |
| volumeType | Type de volume sur lequel l’opération de chiffrement est effectuée. Les valeurs valides sont _Système d’exploitation_, _Données_ et _Tous_. 
| forceUpdateTag | Passez à une valeur unique comme un GUID chaque fois que l’opération doit être exécutée de force. |
| resizeOSDisk | Si la partition du système d’exploitation doit être redimensionnée pour occuper tout le disque dur virtuel du système d’exploitation avant de fractionner le volume système. |
| location | Emplacement pour toutes les ressources. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Chiffrer le groupe de machines virtuelles identiques

Les [groupes identiques de machines virtuelles Azure](../virtual-machine-scale-sets/overview.md) vous permettent de créer et de gérer un groupe de machines virtuelles identiques et disposant d’une charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini. Utilisez la CLI ou Azure PowerShell pour chiffrer un groupe de machines virtuelles identiques.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Chiffrer des groupes de machines virtuelles identiques à l’aide d’Azure PowerShell

Utilisez le [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) applet de commande pour activer le chiffrement sur un jeu de mise à l’échelle de machine virtuelle Windows. Le groupe de ressources, machines virtuelles identiques et coffre de clés doivent déjà créées comme composants requis.

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution** :
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Obtenir le statut de chiffrement pour un groupe de machines virtuelles identiques :** Utilisez le [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) applet de commande.
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Désactiver l’état de chiffrement d’un groupe de machines virtuelles identiques :** Utilisez le [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) applet de commande. 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Chiffrer des groupes de machines virtuelles identiques avec Azure CLI

Utilisez l’[activation de chiffrage az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-enable) pour activer le chiffrement sur un groupe de machines virtuelles identiques Windows. Si vous définissez la stratégie de mise à niveau du groupe identique sur mode manuel, démarrez le chiffrement avec [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Le groupe de ressources, machines virtuelles identiques et coffre de clés doivent déjà créées comme composants requis.

-  **Chiffrer un jeu de mise à l’échelle de machine virtuelle en cours d’exécution**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Chiffrer une échelle de machine virtuelle en cours d’exécution définie à l’aide de la KEK à encapsuler la clé**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Obtenir le statut de chiffrement pour un groupe de machines virtuelles identiques :** utilisez [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Désactiver l’état de chiffrement d’un groupe de machines virtuelles identiques :** Utilisez [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable).
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Modèles Azure Resource Manager pour les groupe de machines virtuelles identiques Windows

Pour chiffrer ou déchiffrer les groupe de machines virtuelles identiques Windows, utilisez les modèles Azure Resource Manager et les instructions ci-dessous :

- [Activer le chiffrement sur un jeu de mise à l’échelle de machine virtuelle Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Désactiver le chiffrement sur un jeu de mise à l’échelle de machine virtuelle Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Cliquez sur **Déployer dans Azure**.
     2. Renseignez les champs obligatoires, puis acceptez les conditions générales.
     3. Sélectionnez **Acheter** pour déployer le modèle.

## <a name="bkmk_VHDpre"> </a> Nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels et de clés de chiffrement chiffrés par le client

Dans ce scénario, vous pouvez activer le chiffrement avec des applets de commande PowerShell ou avec des commandes CLI. 

Utilisez les instructions de l’annexe pour la préparer d’images préchiffrées qui peuvent être utilisées dans Azure. Une fois l’image créée, vous pouvez suivre la procédure décrite dans la section suivante pour créer une machine virtuelle Azure chiffrée.

* [Préparer un disque dur virtuel Windows déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Préparer un disque dur virtuel Linux déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois qu’une sauvegarde est effectuée, l’applet de commande Set-AzVMDiskEncryptionExtension peut être utilisé pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. La commande Set-AzVMDiskEncryptionExtension échoue sur les machines virtuelles avec disque managé en fonction jusqu'à ce qu’une sauvegarde a été effectuée et que ce paramètre a été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 


### <a name="bkmk_VHDprePSH"> </a> Chiffrer des machines virtuelles avec des disques durs virtuels préchiffrés avec Azure PowerShell
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré à l’aide de l’applet de commande PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). L’exemple ci-dessous vous montre certains paramètres courants. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Activer le chiffrement sur un disque de données nouvellement ajouté
Vous pouvez [ajouter un nouveau disque à une machine virtuelle Windows avec PowerShell](../virtual-machines/windows/attach-disk-ps.md) ou via le [portail Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure PowerShell
 Quand vous utilisez PowerShell pour chiffrer un nouveau disque de machines virtuelles Windows, vous devez spécifier une nouvelle version de la séquence. La version de la séquence doit être unique. Le script ci-dessous génère un GUID pour la version de la séquence. Dans certains cas, un disque de données nouvellement ajouté peut être chiffré automatiquement par l’extension Azure Disk Encryption. Le chiffrement automatique se produit généralement quand la machine virtuelle redémarre après la mise en ligne du nouveau disque. Cela est généralement dû au fait que « Tous » était spécifié pour le type de volume quand le chiffrement de disque a été précédemment exécuté sur la machine virtuelle. Si le chiffrement automatique se produit sur un disque de données nouvellement ajoutées, nous vous recommandons d’exécuter l’applet de commande Set-AzVmDiskEncryptionExtension à nouveau avec la nouvelle version de la séquence. Si votre nouveau disque de données est automatiquement chiffré et que vous ne voulez pas qu’il le soit, déchiffrez d’abord tous les lecteurs, puis chiffrez-les à nouveau avec une nouvelle version de séquence spécifiant le système d’exploitation pour le type de volume. 
  
 

-  **Chiffrer une machine virtuelle en cours d’exécution :** Le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzVMDiskEncryptionExtension. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. Remplacez MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM et MySecureVault par vos valeurs. Cet exemple utilise « All » pour le paramètre VolumeType, qui inclut les volumes de données et de systèmes d’exploitation. Si vous souhaitez uniquement chiffrer le volume de systèmes d’exploitation, utilisez « OS » pour le paramètre VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK :** Cet exemple utilise « All » pour le paramètre VolumeType, qui inclut les volumes de données et de systèmes d’exploitation. Si vous souhaitez uniquement chiffrer le volume de systèmes d’exploitation, utilisez « OS » pour le paramètre VolumeType.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure CLI
 La commande Azure CLI vous propose automatiquement une nouvelle version de la séquence quand vous exécutez la commande pour activer le chiffrement. L’exemple utilise « All » pour le paramètre volume-type. Vous devrez peut-être remplacer le paramètre volume-type par « OS » si vous ne chiffrez que le disque du système d’exploitation. Contrairement à la syntaxe de PowerShell, l’interface CLI ne nécessite pas que l’utilisateur fournisse une version de séquence unique lors de l’activation du chiffrement. L’interface CLI génère automatiquement et utilise sa propre valeur de version de séquence unique.   

-  **Chiffrer une machine virtuelle en cours d’exécution :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Chiffrer une machine virtuelle à l’aide de la KEK :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Désactiver le chiffrement
Vous pouvez désactiver le chiffrement avec Azure PowerShell, Azure CLI ou un modèle Resource Manager. La désactivation du chiffrement de disque de données sur la machine virtuelle Windows lorsque les disques du système d’exploitation et de données ont été chiffrés ne fonctionne pas comme prévu. Désactivez plutôt le chiffrement sur tous les disques.

- **Désactiver le chiffrement de disque avec Azure PowerShell :** Pour désactiver le chiffrement, utilisez la [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) applet de commande. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Désactiver le chiffrement avec Azure CLI :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Désactiver le chiffrement avec un modèle Resource Manager :** 

    1. Cliquez sur **Déployer sur Azure** dans le modèle [Désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement, la machine virtuelle, le type de volume, les conditions juridiques et le contrat.
    3.  Cliquez sur **Acheter** pour désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour Linux](azure-security-disk-encryption-linux.md)
