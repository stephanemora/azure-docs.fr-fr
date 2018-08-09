---
title: Azure Disk Encryption pour les machines virtuelles IaaS Linux | Microsoft Docs
description: Cet article fournit des instructions sur l’activation Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Linux.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 071ee0c3-2e6c-4ea9-bfc7-908865629144
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 92c9fce39a0fe068e29cf0a75b3c786891876dee
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392996"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Activer Azure Disk Encryption pour les machines virtuelles IaaS Linux 

Il existe de nombreux scénarios permettant d’activer le chiffrement de disque et les étapes peuvent varier selon le scénario. Les sections suivantes décrivent ces scénarios de façon plus détaillée pour les machines virtuelles IaaS Linux. Avant de pouvoir utiliser le chiffrement de disque, les [prérequis d’Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) doivent être satisfaits et vous devez passer en revue la section [Prérequis supplémentaires pour les machines virtuelles IaaS Linux](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq).

Prenez un [instantané](../virtual-machines/windows/snapshot-copy-managed-disk.md) et/ou faites une sauvegarde avant de chiffrer les disques. Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Les machines virtuelles avec des disques managés imposent une sauvegarde avant que le chiffrement soit effectué. Une fois la sauvegarde effectuée, vous pouvez utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques managés en spécifiant le paramètre -skipVmBackup. Pour plus d’informations sur la façon de sauvegarder et de restaurer des machines virtuelles chiffrées, consultez l’article [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 >Pour garantir que les secrets de chiffrement ne franchissent pas les limites régionales, Azure Disk Encryption exige que le coffre de clés se trouve dans la même région que les machines virtuelles. Créez et utilisez un coffre de clés situé dans la même région que la machine virtuelle à chiffrer.</br></br>

> Lors du chiffrement des volumes de système d’exploitation Linux, le processus peut prendre quelques heures. Il est normal que la durée du chiffrement des volumes de système d’exploitation Linux soit supérieure à celle des volumes de données. 

>La désactivation du chiffrement sur les machines virtuelles Linux est prise en charge seulement pour les volumes de données. Elle n’est pas prise en charge sur les volumes de données ou de système d’exploitation si le volume du système d’exploitation a été chiffré.  


## <a name="bkmk_NewLinux"></a> Déployer une nouvelle machine virtuelle IaaS Linux avec le chiffrement de disque activé 

1. Utilisez le [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) pour créer une machine virtuelle IaaS Linux chiffrée. Le modèle crée une machine virtuelle RedHat Linux 7.2 avec une baie RAID-0 de 200 Go et le chiffrement de disque complet en utilisant des disques managés. Dans l’article [Forum aux questions ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport), vous pouvez remarquer que certaines distributions Linux prennent en charge le chiffrement seulement pour les disques de données. Cependant, ce modèle vous donne la possibilité de vous familiariser avec les modèles de déploiement et de vérifier l’état du chiffrement avec plusieurs méthodes. 
 
1. Cliquez sur **Déployer sur Azure** pour le modèle Azure Resource Manager.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les paramètres, les conditions légales et le contrat. Cliquez sur **Créer** pour activer le chiffrement sur la machine virtuelle IaaS existante ou en cours d’exécution.

3. Après avoir déployé le modèle, vérifiez l’état du chiffrement de la machine virtuelle avec la méthode de votre choix :
     - Vérifiez avec l’interface CLI en utilisant la commande [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Vérifiez avec Azure PowerShell en utilisant l’applet de commande [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Sélectionnez la machine virtuelle, puis cliquez sur **Disques** sous le titre **Paramètres** pour vérifier l’état du chiffrement dans le portail. Dans le graphique sous **Chiffrement**, vous voyez s’il est activé. 

| Paramètre | Description |
| --- | --- |
| ID de client AAD | ID de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| Secret du client AAD | Clé secrète de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| Nom du coffre de clés | Nom du coffre de clés où la clé doit être placée. |
| Groupe de ressources du coffre de clés | Groupe de ressources du coffre de clés. |


## <a name="bkmk_RunningLinux"> </a> Activer le chiffrement sur une machine virtuelle IaaS Linux existante ou en cours d’exécution
Dans ce scénario, vous pouvez activer le chiffrement en utilisant le modèle Resource Manager, des applets de commande PowerShell ou des commandes CLI. 

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois la sauvegarde effectuée, il est possible d’utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. La commande Set-AzureRmVMDiskEncryptionExtension échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Activer le chiffrement sur une machine virtuelle Linux existante ou en cours d’exécution avec Azure CLI 
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré en installant et en utilisant l’outil en ligne de commande [Azure CLI 2.0](/cli/azure). Vous pouvez l’ouvrir dans le navigateur avec [Azure Cloud Shell](../cloud-shell/overview.md), ou vous pouvez l’installer sur votre ordinateur local et l’utiliser dans une session PowerShell. Pour activer le chiffrement sur des machines virtuelles IaaS Linux existantes ou en cours d’exécution dans Azure, utilisez les commandes CLI suivantes :

Utilisez la commande [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure.

-  **Chiffrer une machine virtuelle en cours d’exécution avec un secret client :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Chiffrer une machine virtuelle en cours d’exécution avec une clé KEK pour wrapper le secret client :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> </br>
La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 

- **Vérifier que les disques sont chiffrés :** pour vérifier l’état du chiffrement d’une machine virtuelle IaaS, utilisez la commande [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Désactiver le chiffrement :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). La désactivation du chiffrement est autorisée seulement sur les volumes de données pour les machines virtuelles Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Activer le chiffrement sur une machine virtuelle Linux existante ou en cours d’exécution avec PowerShell
Utilisez la commande [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) pour activer le chiffrement sur une machine virtuelle IaaS en cours d’exécution dans Azure. 

-  **Chiffrer une machine virtuelle en cours d’exécution avec un secret client :** le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzureRmVMDiskEncryptionExtension. Les prérequis sont que le groupe de ressources, la machine virtuelle, le coffre de clés, l’application AAD et le secret client doivent déjà avoir été créés. Remplacez MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID et My-AAD-client-secret par vos valeurs. Il peut être nécessaire d’ajouter le paramètre -VolumeType si vous chiffrez des disques de données et non pas le disque du système d’exploitation. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK pour wrapper le secret client :** Azure Disk Encryption vous permet de spécifier une clé existante de votre coffre de clés pour wrapper les secrets de chiffrement de disque qui ont été générés lors de l’activation du chiffrement. Quand une clé de chiffrement principale est spécifiée, Azure Disk Encryption utilise cette clé pour wrapper les secrets de chiffrement avant d’écrire dans Key Vault. Il peut être nécessaire d’ajouter le paramètre -VolumeType si vous chiffrez des disques de données et non pas le disque du système d’exploitation. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 
    
- **Vérifier que les disques sont chiffrés :** pour vérifier l’état du chiffrement d’une machine virtuelle IaaS, utilisez l’applet de commande [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Désactiver le chiffrement de disque :** pour désactiver le chiffrement, utilisez l’applet de commande [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). La désactivation du chiffrement est autorisée seulement sur les volumes de données pour les machines virtuelles Linux.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Activer le chiffrement sur une machine virtuelle IaaS Linux existante ou en cours d’exécution avec un modèle

Vous pouvez activer le chiffrement de disque sur une machine virtuelle IaaS Linux existante ou en cours d’exécution dans Azure à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Cliquez sur **Déployer sur Azure** sur le modèle de démarrage rapide Azure.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les paramètres, les conditions légales et le contrat. Cliquez sur **Créer** pour activer le chiffrement sur la machine virtuelle IaaS existante ou en cours d’exécution.

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour les machines virtuelles existantes ou en cours d’exécution qui utilisent un ID de client Azure AD :

| Paramètre | Description |
| --- | --- |
| AADClientID | ID de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| AADClientSecret | Clé secrète de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| keyVaultName | Nom du coffre de clés où la clé doit être chargée. Vous pouvez l’obtenir avec l’applet de commande `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  keyEncryptionKeyURL | URL de la clé de chiffrement principale utilisée pour chiffrer la clé générée. Ce paramètre est facultatif si vous sélectionnez **nokek** dans la liste déroulante UseExistingKek. Si vous sélectionnez **kek** dans la liste déroulante UseExistingKek, vous devez entrer la valeur _keyEncryptionKeyURL_. |
| volumeType | Type de volume sur lequel l’opération de chiffrement est effectuée. Les valeurs valides prises en charge sont _OS_ ou _All_ (voir les distributions Linux et leurs versions prises en charge pour les disques de système d’exploitation et de données plus haut dans la section des prérequis). |
| sequenceVersion | Version de séquence de l’opération BitLocker. Incrémentez ce numéro de version à chaque fois qu’une opération de chiffrement de disque est exécutée sur la même machine virtuelle. |
| vmName | Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée. |
| phrase secrète | Saisissez une phrase secrète forte comme clé de chiffrement de données. |



## <a name="bkmk_EFA"> </a>Utiliser la fonctionnalité EncryptFormatAll pour les disques de données sur les machines virtuelles IaaS Linux
Le paramètre **EncryptFormatAll** réduit le temps de chiffrement des disques de données Linux. Les partitions répondant à certains critères sont formatées (avec leur système de fichiers actuel). Ensuite, elles sont remontées là où elles se trouvaient avant l’exécution de la commande. Si vous voulez exclure un disque de données répondant aux critères, vous pouvez le démonter avant d’exécuter la commande.

 Après l’exécution de cette commande, tous les lecteurs qui ont été montés précédemment sont formatés. La couche de chiffrement est ensuite démarrée sur le lecteur qui est maintenant vide. Quand cette option est sélectionnée, le disque de ressources éphémère attaché à la machine virtuelle est également chiffré. Si le disque éphémère est réinitialisé, il est reformaté et rechiffré par la solution Azure Disk Encryption dès que l’occasion s’en présente.

>[!WARNING]
> EncryptFormatAll ne doit pas être utilisé quand des données indispensables se trouvent sur les volumes de données d’une machine virtuelle. Vous pouvez exclure des disques du chiffrement en les démontant. Vous devez d’abord essayer EncryptFormatAll sur une machine virtuelle de test, comprendre le paramètre de la fonctionnalité et son implication, avant de l’essayer sur la machine virtuelle de production. L’option EncryptFormatAll formate le disque de données et toutes ses données seront perdues. Avant de continuer, vérifiez que les disques que vous voulez exclure sont correctement démontés. </br></br>
 >Si vous définissez ce paramètre lors de la mise à jour des paramètres de chiffrement, il peut entraîner un redémarrage avant le chiffrement proprement dit. Dans ce cas, vous pouvez aussi supprimer du fichier fstab le disque que vous ne voulez pas formater. De même, vous devez ajouter la partition que vous voulez chiffrer-formater au fichier fstab avant de lancer l’opération de chiffrement. 

### <a name="bkmk_EFACriteria"> </a> Critères pour EncryptFormatAll
Ce paramètre fait que la commande parcourt toutes les partitions et les chiffre, à condition qu’elles répondent à **tous** les critères ci-dessous : 
- Elle n’est pas une partition root/de système d’exploitation/de démarrage
- Elle n’est pas déjà chiffrée
- Elle n’est pas un volume BEK
- Elle est montée


### <a name="bkmk_EFATemplate"> </a> Utiliser le paramètre EncryptFormatAll avec un modèle
Pour utiliser l’option EncryptFormatAll, utilisez un modèle Azure Resource Manager préexistant qui chiffre une machine virtuelle Linux et modifiez le champ **EncryptionOperation** pour la ressource AzureDiskEncryption.

1. Par exemple, utilisez le [modèle Resource Manager pour chiffrer une machine virtuelle IaaS Linux en cours d’exécution](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Cliquez sur **Déployer sur Azure** sur le modèle de démarrage rapide Azure.
3. Changez **EncryptionOperation** en remplaçant **EnableEncryption** par **EnableEncryptionFormatAl**
4. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les autres paramètres, les conditions légales et le contrat. Cliquez sur **Créer** pour activer le chiffrement sur la machine virtuelle IaaS existante ou en cours d’exécution.


### <a name="bkmk_EFAPSH"> </a> Utiliser le paramètre EncryptFormatAll avec une applet de commande PowerShell
Utilisez l’applet de commande [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) avec le [paramètre EncryptFormatAll](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Chiffrer une machine virtuelle en cours d’exécution avec un secret client et EncryptFormatAll :** à titre d’exemple, le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzureRmVMDiskEncryptionExtension avec le paramètre EncryptFormatAll. Les prérequis sont que le groupe de ressources, la machine virtuelle, le coffre de clés, l’application AAD et le secret client doivent déjà avoir été créés. Remplacez MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID et My-AAD-client-secret par vos valeurs.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
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
    
    4. Exécutez l’applet de commande PowerShell Set-AzureRmVMDiskEncryptionExtension avec -EncryptFormatAll pour chiffrer ces disques.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResouceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Configurez le gestionnaire de volumes logiques sur ces nouveaux disques. Notez que les lecteurs chiffrés sont déverrouillés une fois que la machine virtuelle a fini de démarrer. Ainsi, le montage du gestionnaire de volumes logiques devra également être retardé.




## <a name="bkmk_VHDpre"> </a> Nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels et de clés de chiffrement chiffrés par le client
Dans ce scénario, vous pouvez activer le chiffrement à l’aide du modèle Resource Manager, des applets de commande PowerShell ou des commandes CLI. Les sections ci-dessous décrivent de façon plus détaillée le modèle Resource Manager et les commandes CLI. 

Utilisez les instructions de l’annexe pour la préparer d’images préchiffrées qui peuvent être utilisées dans Azure. Une fois l’image créée, vous pouvez suivre la procédure décrite dans la section suivante pour créer une machine virtuelle Azure chiffrée.

* [Préparer un disque dur virtuel Windows déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Préparer un disque dur virtuel Linux déjà chiffré](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque managé en dehors d’Azure Disk Encryption et avant de l’activer. Vous pouvez prendre un instantané du disque managé à partir du portail ou utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-encryption.md). Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement. Une fois la sauvegarde effectuée, il est possible d’utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup. La commande Set-AzureRmVMDiskEncryptionExtension échoue sur les machines virtuelles basées sur des disques managés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié. 
>
>Le chiffrement ou la désactivation du chiffrement peut entraîner le redémarrage de la machine virtuelle. 



### <a name="bkmk_VHDprePSH"> </a> Utiliser Azure PowerShell pour chiffrer des machines virtuelles IaaS avec des disques durs virtuels préchiffrés 
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré avec l’applet de commande PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). L’exemple ci-dessous vous montre certains paramètres courants. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> Utiliser le modèle Resource Manager pour chiffrer des machines virtuelles IaaS avec des disques durs virtuels préchiffrés 
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déployer sur Azure**.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les paramètres, les conditions légales et le contrat. Cliquez sur **Créer** pour activer le chiffrement sur la nouvelle machine virtuelle IaaS.

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour votre disque dur virtuel chiffré :

| Paramètre | Description |
| --- | --- |
| newStorageAccountName | Nom du compte de stockage pour stocker le disque dur virtuel du système d’exploitation chiffré. Ce compte de stockage doit avoir été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle. |
| osVhdUri | URI du disque dur virtuel du système d’exploitation à partir du compte de stockage. |
| osType | Type de système d’exploitation (Windows/Linux). |
| virtualNetworkName | Nom du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. Le nom doit avoir été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle. |
| subnetName | Nom du sous-réseau du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. |
| vmSize | Taille de la machine virtuelle. Actuellement, seules les séries A, D et G standard sont prises en charge. |
| keyVaultResourceID | L’ID de ressource qui identifie la ressource de coffre de clés dans Azure Resource Manager. Vous pouvez l’obtenir en utilisant l’applet de commande PowerShell `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` ou la commande Azure CLI `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL de la clé de chiffrement de disque définie dans le coffre de clés. |
| keyVaultKekUrl | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé de chiffrement de disque générée. |
| vmName | Nom de la machine virtuelle IaaS. |
## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Activer le chiffrement sur un disque de données nouvellement ajouté
Vous pouvez ajouter un nouveau disque de données avec [az vm disk attach](../virtual-machines/linux/add-disk.md) ou [via le portail Azure](../virtual-machines/linux/attach-disk-portal.md). Avant de pouvoir chiffrer, vous devez d’abord monter le disque de données nouvellement attaché. Vous devez demander le chiffrement du lecteur de données, car le lecteur sera inutilisable pendant que le chiffrement est en cours. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure CLI
 La commande Azure CLI vous propose automatiquement une nouvelle version de la séquence quand vous exécutez la commande pour activer le chiffrement. 
-  **Chiffrer une machine virtuelle en cours d’exécution avec un secret client :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Chiffrer une machine virtuelle en cours d’exécution avec une clé KEK pour wrapper le secret client :**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Activer le chiffrement sur un disque nouvellement ajouté avec Azure PowerShell
 Quand vous utilisez PowerShell pour chiffrer un nouveau disque pour Linux, vous devez spécifier une nouvelle version de la séquence. La version de la séquence doit être unique. Le script ci-dessous génère un GUID pour la version de la séquence. 
 

-  **Chiffrer une machine virtuelle en cours d’exécution avec un secret client :** le script ci-dessous initialise vos variables et exécute l’applet de commande Set-AzureRmVMDiskEncryptionExtension. Les prérequis sont que le groupe de ressources, la machine virtuelle, le coffre de clés, l’application AAD et le secret client doivent déjà avoir été créés. Remplacez MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID et My-AAD-client-secret par vos valeurs. Le paramètre -VolumeType est défini pour les disques de données et non pas pour le disque du système d’exploitation. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Chiffrer une machine virtuelle en cours d’exécution avec la clé KEK pour wrapper le secret client :** Azure Disk Encryption vous permet de spécifier une clé existante de votre coffre de clés pour wrapper les secrets de chiffrement de disque qui ont été générés lors de l’activation du chiffrement. Quand une clé de chiffrement principale est spécifiée, Azure Disk Encryption utilise cette clé pour wrapper les secrets de chiffrement avant d’écrire dans Key Vault. Il peut être nécessaire d’ajouter le paramètre -VolumeType si vous chiffrez des disques de données et non pas le disque du système d’exploitation. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > La syntaxe de la valeur du paramètre disk-encryption-keyvault est la chaîne d’identificateur complet : /subscriptions/[GUID_ID_abonnement]/resourceGroups/[nom_groupe_ressources]/providers/Microsoft.KeyVault/vaults/[nom_coffre_clés]</br> La syntaxe de la valeur du paramètre key-encryption-key est l’URI complet de la clé KEK comme dans : https://[nom_coffre_clés].vault.azure.net/keys/[nom_clé_kek]/[ID_unique_clé_kek] 


## <a name="disable-encryption-for-linux-vms"></a>Désactiver le chiffrement pour les machines virtuelles Linux
Vous pouvez désactiver le chiffrement avec Azure PowerShell, Azure CLI ou un modèle Resource Manager. 

>[!IMPORTANT]
>La désactivation du chiffrement avec Azure Disk Encryption sur les machines virtuelles Linux est prise en charge seulement pour les volumes de données. Elle n’est pas prise en charge sur les volumes de données ou de système d’exploitation si le volume du système d’exploitation a été chiffré.  

- **Désactiver le chiffrement de disque avec Azure PowerShell :** pour désactiver le chiffrement, utilisez l’applet de commande [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Désactiver le chiffrement avec Azure CLI :** pour désactiver le chiffrement, utilisez la commande [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Désactiver le chiffrement avec un modèle Resource Manager :** utilisez le modèle [Désactiver le chiffrement sur une machine virtuelle Linux en cours d’exécution](https://aka.ms/decrypt-linuxvm) pour désactiver le chiffrement.
     1. Cliquez sur **Déployer dans Azure**.
     2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement, les conditions juridiques et le contrat.
     3.  Cliquez sur **Acheter** pour désactiver le chiffrement de disque sur une machine virtuelle Windows en cours d’exécution. 


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour Windows](azure-security-disk-encryption-windows.md)
