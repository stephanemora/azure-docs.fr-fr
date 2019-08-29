---
title: Activer Azure Disk Encryption pour les machines virtuelles IaaS Linux
description: Cet article fournit des instructions sur l’activation Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/05/2019
ms.custom: seodec18
ms.openlocfilehash: bc9e8af907092a2d2929e50284f048510ff6210b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065952"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Activer Azure Disk Encryption pour les machines virtuelles IaaS Linux 

Il existe de nombreux scénarios permettant d’activer le chiffrement de disque et les étapes peuvent varier selon le scénario. Les sections suivantes décrivent ces scénarios de façon plus détaillée pour les machines virtuelles IaaS Linux. Avant de pouvoir utiliser le chiffrement de disque, les [prérequis d’Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) doivent être satisfaits et vous devez passer en revue la section [Prérequis supplémentaires pour les machines virtuelles IaaS Linux](azure-security-disk-encryption-prerequisites.md#additional-prerequisites-for-linux-iaas-vms).

Prenez un [instantané](../virtual-machines/windows/snapshot-copy-managed-disk.md) et/ou faites une sauvegarde avant de chiffrer les disques. Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue lors du chiffrement. Les machines virtuelles avec des disques managés imposent une sauvegarde avant que le chiffrement soit effectué. Une fois la sauvegarde effectuée, vous pouvez utiliser l’applet de commande Set-AzVMDiskEncryptionExtension pour chiffrer des disques managés en spécifiant le paramètre -skipVmBackup. Pour plus d’informations sur la façon de sauvegarder et de restaurer des machines virtuelles chiffrées, consultez l’article [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Si vous avez déjà utilisé [Azure Disk Encryption avec une application Azure AD](azure-security-disk-encryption-prerequisites-aad.md) pour chiffrer cette machine virtuelle, vous devrez continuer à utiliser cette option. Vous ne pouvez pas utiliser [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) sur cette machine virtuelle chiffrée car un tel scénario n’est pas pris en charge. Autrement dit, l’utilisation d’une autre application que l’application AAD pour cette machine virtuelle n’est pas encore prise en charge.
 > - Azure Disk Encryption a besoin que Key Vault et les machines virtuelles se trouvent dans la même région. Créez et utilisez un coffre de clés situé dans la même région que la machine virtuelle à chiffrer.
> - Lors du chiffrement de volumes de système d’exploitation Linux, la machine virtuelle ne devrait pas être disponible. Nous vous recommandons vivement d'éviter les connexions SSH pendant le chiffrement afin d'éviter tout problème risquant de bloquer les fichiers ouverts qui devront être accessibles pendant le processus de chiffrement. Pour vérifier la progression, vous pouvez utiliser la commande [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). Ce processus peut prendre quelques heures pour un volume de système d’exploitation de 30 Go, et du temps supplémentaire pour le chiffrement des volumes de données. La durée de chiffrement de volume de données est proportionnelle à la taille et à la quantité des volumes de données, sauf si l’option « EncryptFormatAll » est utilisée. 
> - La désactivation du chiffrement sur les machines virtuelles Linux est prise en charge seulement pour les volumes de données. Elle n’est pas prise en charge sur les volumes de données ou de système d’exploitation si le volume du système d’exploitation a été chiffré.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Activer le chiffrement sur une machine virtuelle IaaS Linux existante ou en cours d’exécution
Dans ce scénario, vous pouvez activer le chiffrement en utilisant le modèle Resource Manager, des applets de commande PowerShell ou des commandes CLI. Si vous avez besoin d’informations de schéma pour l’extension de machine virtuelle, consultez l’article [Azure Disk Encryption pour extension Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois la sauvegarde effectuée, il est possible d’utiliser la cmdlet Set-AzVMDiskEncryptionExtension pour chiffrer des disques managés en spécifiant le paramètre -skipVmBackup. La commande Set-AzVMDiskEncryptionExtension échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Activer le chiffrement sur une machine virtuelle Linux existante ou en cours d’exécution avec Azure CLI 
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré en installant et en utilisant l’outil en ligne de commande [Azure CLI 2.0](/cli/azure). Vous pouvez l’ouvrir dans le navigateur avec [Azure Cloud Shell](../cloud-shell/overview.md), ou vous pouvez l’installer sur votre ordinateur local et l’utiliser dans une session PowerShell. Pour activer le chiffrement sur des machines virtuelles IaaS Linux existantes ou en cours d’exécution dans Azure, utilisez les commandes CLI suivantes :

Utilisez la commande [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure.

- **Chiffrer une machine virtuelle en cours d’exécution :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br>
La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Vérifier que les disques sont chiffrés :** Pour vérifier l’état du chiffrement d’une machine virtuelle IaaS, utilisez la commande [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Désactiver le chiffrement :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). La désactivation du chiffrement est autorisée seulement sur les volumes de données pour les machines virtuelles Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Activer le chiffrement sur une machine virtuelle Linux existante ou en cours d’exécution avec PowerShell
Utilisez la commande [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure. Prenez un [instantané](../virtual-machines/windows/snapshot-copy-managed-disk.md) et/ou effectuez une sauvegarde de la machine virtuelle avec [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md) avant de chiffrer les disques. Le paramètre -skipVmBackup est déjà spécifié dans les scripts PowerShell pour chiffrer une machine virtuelle Linux en cours d'exécution.

-  **Chiffrer une machine virtuelle en cours d’exécution :** Le script ci-dessous initialise vos variables et exécute la cmdlet Set-AzVMDiskEncryptionExtension. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. Remplacez MyVirtualMachineResourceGroup, MySecureVM et MySecureVault par vos valeurs. Modifiez le paramètre -VolumeType pour spécifier les disques que vous chiffrez.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK :** Il peut être nécessaire d’ajouter le paramètre -VolumeType si vous chiffrez des disques de données et non le disque du système d’exploitation. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 
    
- **Vérifier que les disques sont chiffrés :** Pour vérifier l’état du chiffrement d’une machine virtuelle IaaS, utilisez la cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus). 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Désactiver le chiffrement de disque :** Pour désactiver le chiffrement, utilisez l’applet de commande [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). La désactivation du chiffrement est autorisée seulement sur les volumes de données pour les machines virtuelles Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Activer le chiffrement sur une machine virtuelle IaaS Linux existante ou en cours d’exécution avec un modèle

Vous pouvez activer le chiffrement de disque sur une machine virtuelle IaaS Linux existante ou en cours d’exécution dans Azure à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Cliquez sur **Déployer sur Azure** sur le modèle de démarrage rapide Azure.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les paramètres, les conditions légales et le contrat. Cliquez sur **Créer** pour activer le chiffrement sur la machine virtuelle IaaS existante ou en cours d’exécution.

Le tableau suivant répertorie des paramètres du modèle Resource Manager pour les machines virtuelles existantes ou en cours d’exécution :

| Paramètre | Description |
| --- | --- |
| vmName | Nom de la machine virtuelle d’exécution de l’opération de chiffrement. |
| keyVaultName | Nom du coffre de clés dans lequel la clé BitLocker doit être téléchargée. Vous pouvez l’obtenir avec la cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou la commande Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`|
| keyVaultResourceGroup | Nom du groupe de ressources qui contient le coffre de clés|
|  keyEncryptionKeyURL | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Ce paramètre est facultatif si vous sélectionnez **nokek** dans la liste déroulante UseExistingKek. Si vous sélectionnez **kek** dans la liste déroulante UseExistingKek, vous devez entrer la valeur _keyEncryptionKeyURL_. |
| volumeType | Type de volume sur lequel l’opération de chiffrement est effectuée. Les valeurs valides sont _Système d’exploitation_, _Données_ et _Tous_. 
| forceUpdateTag | Passez à une valeur unique comme un GUID chaque fois que l’opération doit être exécutée de force. |
| resizeOSDisk | Si la partition du système d’exploitation doit être redimensionnée pour occuper tout le disque dur virtuel du système d’exploitation avant de fractionner le volume système. |
| location | Emplacement pour toutes les ressources. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Chiffrer le groupe de machines virtuelles identiques
Les [groupes identiques de machines virtuelles Azure](../virtual-machine-scale-sets/overview.md) vous permettent de créer et de gérer un groupe de machines virtuelles identiques et disposant d’une charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini. Utilisez la CLI ou Azure PowerShell pour chiffrer un groupe de machines virtuelles identiques. Seul le chiffrement des disques de données est pris en charge sur les groupes de machines virtuelles Linux identiques.

Un exemple de fichier de traitement de chiffrement de disque de données d’un groupe identique Linux est disponible [ici](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). Cet exemple crée un groupe de ressources, un groupe identique Linux, monte un disque de données de 5 Go et chiffre le groupe de machines virtuelles identiques.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Chiffrer des groupes de machines virtuelles identiques avec Azure CLI

Utilisez l’[activation de chiffrage az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-enable) pour activer le chiffrement sur un groupe de machines virtuelles identiques Windows. Si vous définissez la stratégie de mise à niveau du groupe identique sur mode manuel, démarrez le chiffrement avec [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. 

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution**
    ```azurecli-interactive
    az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault"
    ```

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution avec la KEK pour inclure la clé dans un wrapper**
     ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault"
     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Obtenir le statut de chiffrement pour un groupe de machines virtuelles identiques :** utilisez [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
    az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Désactiver l’état de chiffrement d’un groupe de machines virtuelles identiques :** utilisez [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
    az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Chiffrer des groupes de machines virtuelles identiques à l’aide d’Azure PowerShell

Utilisez l’applet de commande [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) pour activer le chiffrement sur un groupe de machines virtuelles identiques Windows. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés.

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution** :
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
      ```

-  **Chiffrer un groupe de machines virtuelles identiques en cours d’exécution avec la KEK pour inclure la clé dans un wrapper** :
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $keyEncryptionKeyName = "MyKeyEncryptionKey";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl  -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
      ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Obtenir le statut de chiffrement pour un groupe de machines virtuelles** : Utilisez l’applet de commande [Get-AzVmssVMDiskEncryption](/powershell/module/az.compute/get-azvmssvmdiskencryption).
    
    ```powershell
    Get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Désactiver l’état de chiffrement d’un groupe de machines virtuelles identiques :** Utilisez l’applet de commande [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption). 

    ```powershell
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Modèles Azure Resource Manager pour les groupes de machines virtuelles identiques Linux

Pour chiffrer ou déchiffrer les groupes de machines virtuelles identiques Linux, utilisez les modèles Azure Resource Manager et les instructions ci-dessous :

- [Activer le chiffrement sur un groupe de machines virtuelles identiques Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [Désactiver le chiffrement sur un groupe de machines virtuelles identiques Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Cliquez sur **Déployer dans Azure**.
     2. Renseignez les champs obligatoires, puis acceptez les conditions générales.
     3. Sélectionnez **Acheter** pour déployer le modèle.

## <a name="bkmk_EFA"> </a>Utiliser la fonctionnalité EncryptFormatAll pour les disques de données sur les machines virtuelles IaaS Linux

Le paramètre **EncryptFormatAll** réduit le temps de chiffrement des disques de données Linux. Les partitions répondant à certains critères sont formatées (avec leur système de fichiers actuel). Ensuite, elles sont remontées là où elles se trouvaient avant l’exécution de la commande. Si vous voulez exclure un disque de données répondant aux critères, vous pouvez le démonter avant d’exécuter la commande.

 Après l’exécution de cette commande, tous les lecteurs qui ont été montés précédemment sont formatés. La couche de chiffrement est ensuite démarrée sur le lecteur qui est maintenant vide. Quand cette option est sélectionnée, le disque de ressources éphémère attaché à la machine virtuelle est également chiffré. Si le disque éphémère est réinitialisé, il est reformaté et rechiffré par la solution Azure Disk Encryption dès que l’occasion s’en présente.

>[!WARNING]
> EncryptFormatAll ne doit pas être utilisé quand des données indispensables se trouvent sur les volumes de données d’une machine virtuelle. Vous pouvez exclure des disques du chiffrement en les démontant. Vous devez d’abord essayer EncryptFormatAll sur une machine virtuelle de test, comprendre le paramètre de la fonctionnalité et son implication, avant de l’essayer sur la machine virtuelle de production. L’option EncryptFormatAll formate le disque de données et toutes ses données seront perdues. Avant de continuer, vérifiez que les disques que vous souhaitez exclure sont correctement démontés. </br></br>
 >Si vous définissez ce paramètre lors de la mise à jour des paramètres de chiffrement, il peut entraîner un redémarrage avant le chiffrement proprement dit. Dans ce cas, vous pouvez aussi supprimer du fichier fstab le disque que vous ne voulez pas formater. De même, vous devez ajouter la partition que vous voulez chiffrer-formater au fichier fstab avant de lancer l’opération de chiffrement. 

### <a name="bkmk_EFACriteria"> </a> Critères pour EncryptFormatAll
Ce paramètre fait que la commande parcourt toutes les partitions et les chiffre, à condition qu’elles répondent à **tous** les critères ci-dessous : 
- Elle n’est pas une partition root/de système d’exploitation/de démarrage
- Elle n’est pas déjà chiffrée
- Elle n’est pas un volume BEK
- Elle n’est pas un volume RAID
- Elle n’est pas un volume LVM
- Elle est montée

Chiffrez les disques composant le volume RAID ou LVM au lieu de chiffrer le volume RAID ou LVM lui-même.

### <a name="bkmk_EFAPSH"> </a> Utiliser le paramètre EncryptFormatAll avec Azure CLI
Utilisez la commande [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure.

-  **Chiffrer une machine virtuelle en cours d’exécution avec EncryptFormatAll :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Utiliser le paramètre EncryptFormatAll avec une applet de commande PowerShell
Utilisez l’applet de commande [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) avec le paramètre EncryptFormatAll. 

**Chiffrer une machine virtuelle en cours d’exécution avec EncryptFormatAll :** à titre d’exemple, le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzVMDiskEncryptionExtension avec le paramètre EncryptFormatAll. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. Remplacez MyVirtualMachineResourceGroup, MySecureVM et MySecureVault par vos valeurs.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="bkmk_EFALVM"> </a> Utiliser le paramètre EncryptFormatAll avec le gestionnaire de volumes logiques 
Nous recommandons une installation LVM-on-crypt. Pour les exemples suivants, remplacez le chemin de périphérique et les points de montage par ce qui correspond à votre cas d’utilisation. Cette installation peut se faire comme suit :

- Ajoutez les disques de données qui constitueront la machine virtuelle.
- Formatez, montez et ajoutez ces disques au fichier fstab.

    1. Formatez le disque nouvellement ajouté. Nous utilisons ici des liens symboliques générés par Azure. L’utilisation de liens symboliques évite les problèmes liés à la modification des noms des périphériques. Pour plus d’informations, consultez [Résoudre les problèmes relatifs aux noms des périphériques](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Montez les disques.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Ajoutez-les à fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Exécutez l’applet de commande PowerShell Set-AzVMDiskEncryptionExtension avec -EncryptFormatAll pour chiffrer ces disques.

         ```azurepowershell-interactive
         $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
         
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
         ```

    5. Configurez le gestionnaire de volumes logiques sur ces nouveaux disques. Notez que les lecteurs chiffrés sont déverrouillés une fois que la machine virtuelle a fini de démarrer. Ainsi, le montage du gestionnaire de volumes logiques devra également être retardé.


## <a name="bkmk_VHDpre"> </a> Nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels et de clés de chiffrement chiffrés par le client
Dans ce scénario, vous pouvez activer le chiffrement avec des applets de commande PowerShell ou avec des commandes CLI. 

Utilisez les instructions de l’annexe pour la préparer d’images préchiffrées qui peuvent être utilisées dans Azure. Une fois l’image créée, vous pouvez suivre la procédure décrite dans la section suivante pour créer une machine virtuelle Azure chiffrée.

* [Préparer un disque dur virtuel Windows déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Préparer un disque dur virtuel Linux déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois la sauvegarde effectuée, il est possible d’utiliser la cmdlet Set-AzVMDiskEncryptionExtension pour chiffrer des disques managés en spécifiant le paramètre -skipVmBackup. La commande Set-AzVMDiskEncryptionExtension échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 



### <a name="bkmk_VHDprePSH"> </a> Utiliser Azure PowerShell pour chiffrer des machines virtuelles IaaS avec des disques durs virtuels préchiffrés 
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré avec l’applet de commande PowerShell [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). L’exemple ci-dessous vous montre certains paramètres courants. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Activer le chiffrement sur un disque de données nouvellement ajouté

Vous pouvez ajouter un nouveau disque de données avec [az vm disk attach](../virtual-machines/linux/add-disk.md) ou [via le portail Azure](../virtual-machines/linux/attach-disk-portal.md). Avant de pouvoir chiffrer, vous devez d’abord monter le disque de données nouvellement attaché. Vous devez demander le chiffrement du lecteur de données, car le lecteur sera inutilisable pendant que le chiffrement est en cours. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure CLI

 Si la machine virtuelle a déjà été chiffrée avec « All », le paramètre --volume-type doit rester All. All inclut les disques de système d’exploitation et de données. Si la machine virtuelle a déjà été chiffrée avec un type de volume « OS », le paramètre --volume-type doit être remplacé par All afin que le système d’exploitation et le nouveau disque de données soient inclus. Si la machine virtuelle a été chiffrée avec uniquement le type de volume « Data », le paramètre « Data » peut être conservé comme illustré ci-dessous. L’ajout et l’attachement d’un nouveau disque de données à une machine virtuelle ne constituent pas une préparation suffisante pour le chiffrement. Le disque nouvellement attaché doit aussi être formaté et monté correctement au sein de la machine virtuelle avant l’activation du chiffrement. Sur Linux, le disque doit être monté dans/etc/fstab avec un [nom de l’appareil de bloc persistant](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Contrairement à la syntaxe de PowerShell, l’interface CLI ne nécessite pas que l’utilisateur fournisse une version de séquence unique lors de l’activation du chiffrement. L’interface CLI génère automatiquement et utilise sa propre valeur de version de séquence unique.

-  **Chiffrer des volumes de données d’une machine virtuelle en cours d’exécution :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Chiffrer des volumes de données d’une machine virtuelle en cours d’exécution à l’aide de KEK :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure PowerShell
 Quand vous utilisez PowerShell pour chiffrer un nouveau disque pour Linux, vous devez spécifier une nouvelle version de la séquence. La version de la séquence doit être unique. Le script ci-dessous génère un GUID pour la version de la séquence. Prenez un [instantané](../virtual-machines/windows/snapshot-copy-managed-disk.md) et/ou effectuez une sauvegarde de la machine virtuelle avec [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md) avant de chiffrer les disques. Le paramètre -skipVmBackup est déjà spécifié dans les scripts PowerShell pour chiffrer un disque de données nouvellement ajouté.
 

-  **Chiffrer des volumes de données d’une machine virtuelle en cours d’exécution :** Le script ci-dessous initialise vos variables et exécute la cmdlet Set-AzVMDiskEncryptionExtension. Les prérequis que sont le groupe de ressources, la machine virtuelle et le coffre de clés doivent déjà avoir été créés. Remplacez MyVirtualMachineResourceGroup, MySecureVM et MySecureVault par vos valeurs. Les valeurs acceptables pour le paramètre -VolumeType sont « All », « OS » et « Data ». Si la machine virtuelle a déjà été chiffrée avec un type de volume « OS » ou « All », le paramètre -VolumeType doit être remplacé par All afin que le système d’exploitation et le nouveau disque de données soient inclus.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Chiffrer des volumes de données d’une machine virtuelle en cours d’exécution à l’aide de KEK :** Les valeurs acceptables pour le paramètre -VolumeType sont « All », « OS » et « Data ». Si la machine virtuelle a déjà été chiffrée avec un type de volume « OS » ou « All », le paramètre -VolumeType doit être remplacé par All afin que le système d’exploitation et le nouveau disque de données soient inclus.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressourcesKV]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 


## <a name="disable-encryption-for-linux-vms"></a>Désactiver le chiffrement pour les machines virtuelles Linux
Vous pouvez désactiver le chiffrement avec Azure PowerShell, Azure CLI ou un modèle Resource Manager. 

>[!IMPORTANT]
>La désactivation du chiffrement avec Azure Disk Encryption sur les machines virtuelles Linux est prise en charge seulement pour les volumes de données. Elle n’est pas prise en charge sur les volumes de données ou de système d’exploitation si le volume du système d’exploitation a été chiffré.  

- **Désactiver le chiffrement de disque avec Azure PowerShell :** Pour désactiver le chiffrement, utilisez la cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Désactiver le chiffrement avec Azure CLI :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Désactiver le chiffrement avec un modèle Resource Manager :** utilisez le modèle [Désactiver le chiffrement sur une machine virtuelle Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) pour désactiver le chiffrement.
     1. Cliquez sur **Déployer dans Azure**.
     2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement, les conditions juridiques et le contrat.
     3.  Cliquez sur **Acheter** pour désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution. 


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour Windows](azure-security-disk-encryption-windows.md)
