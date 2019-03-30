---
title: Annexe - Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article constitue l’annexe de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 675ce2de91dd4e53f4945ceef3b174c33be001f6
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649644"
---
# <a name="appendix-for-azure-disk-encryption"></a>Annexe pour Azure Disk Encryption 

Cet article est une annexe de [Azure Disk Encryption pour les machines virtuelles IaaS](azure-security-disk-encryption-overview.md). Lisez d’abord les articles concernant Azure Disk Encryption pour les machines virtuelles IaaS pour bien comprendre le contexte. Cet article décrit comment préparer des disques durs virtuels préchiffrés et d’autres tâches.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-subscription"></a>Connexion à votre abonnement
Avant de commencer, consultez l’article [Prérequis](azure-security-disk-encryption-prerequisites.md). Après avoir satisfait à tous les prérequis, connectez-vous à votre abonnement en exécutant les applets de commande suivantes :

### <a name="bkmk_ConnectPSH"></a> Se connecter à votre abonnement avec PowerShell

1. Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :

     ```powershell
     Connect-AzAccount 
     ```
2. Si vous disposez de plusieurs abonnements et souhaitez spécifier un abonnement à utiliser en particulier, saisissez ce qui suit pour afficher les abonnements de votre compte :
     
     ```powershell
     Get-AzSubscription
     ```
3. Pour spécifier l’abonnement que vous souhaitez utiliser, saisissez :
 
     ```powershell
      Select-AzSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Pour vérifier que l’abonnement configuré est correct, saisissez :
     
     ```powershell
     Get-AzSubscription
     ```
5. Si nécessaire, connectez-vous à Azure AD avec [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Pour confirmer que les applets de commande Azure Disk Encryption sont installées, saisissez :
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Si nécessaire, consultez [Bien démarrer avec Azure PowerShell](/powershell/azure/get-started-azureps) et [Azure AD](/powershell/module/azuread).

### <a name="bkmk_ConnectCLI"></a> Se connecter à votre abonnement avec Azure CLI

1. Connectez-vous à Azure avec [az login](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Si vous voulez sélectionner un locataire sous lequel vous connecter, utilisez :
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Si vous avez plusieurs abonnements et que vous voulez spécifier un de ceux-ci, récupérez la liste de vos abonnements avec [az account list](/cli/azure/account#az-account-list) et spécifiez le compte avec [az account set](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Vérifiez la version installée.
     
     ```azurecli
        az --version
     ``` 

5. Si nécessaire, consultez [Bien démarrer avec Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Exemples de scripts PowerShell pour Azure Disk Encryption 

- **Répertorier toutes les machines virtuelles chiffrées dans votre abonnement**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Répertorier tous les secrets de chiffrement de disque utilisées pour chiffrer les machines virtuelles dans un coffre de clés** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Exécution du script PowerShell de configuration d’Azure Disk Encryption
Si vous êtes déjà familiarisé avec les prérequis d’Azure Disk Encryption, vous pouvez utiliser le [script PowerShell des prérequis d’Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Vous trouverez un exemple d’utilisation de ce script PowerShell dans [Démarrage rapide du chiffrement d’une machine virtuelle](quick-encrypt-vm-powershell.md). Vous pouvez supprimer les commentaires d’une section du script, en commençant à la ligne 211, pour chiffrer tous les disques des machines virtuelles d’un groupe de ressources existant. 

Le tableau suivant présente les paramètres pouvant être utilisés dans le script PowerShell : 


|Paramètre|Description|Est obligatoire|
|------|------|------|
|$resourceGroupName| Nom du groupe de ressources auquel appartient le coffre de clés.  Un groupe de ressources portant ce nom sera créé s’il n’existe pas encore.| True|
|$keyVaultName|Nom du coffre de clés dans lequel les clés de chiffrement doivent être placées. Un coffre portant ce nom sera créé s’il n’existe pas encore.| True|
|$location|Emplacement du coffre de clés. Assurez-vous que le coffre de clés et les machines virtuelles à chiffrer se trouvent au même emplacement. Obtenez une liste des emplacements avec `Get-AzLocation`.|True|
|$subscriptionId|Identificateur de l’abonnement Azure à utiliser.  Vous pouvez obtenir votre ID d’abonnement avec `Get-AzSubscription`.|True|
|$aadAppName|Nom de l’application Azure AD qui sera utilisée pour écrire des secrets dans le coffre de clés. S’il n’existe aucune application de ce nom, une nouvelle application sera créée en utilisant le nom que vous avez saisi. Si cette application existe déjà, appliquez le paramètre aadClientSecret au script.|False|
|$aadClientSecret|Clé secrète client de l’application Azure AD qui a été créée précédemment.|False|
|$keyEncryptionKeyName|Nom de la clé de chiffrement principale facultative dans KeyVault. Une clé portant ce nom sera créée si elle n’existe pas encore.|False|


## <a name="resource-manager-templates"></a>Modèles Resource Manager

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Chiffrer ou déchiffrer des machines virtuelles sans application Azure AD


- [Activer le chiffrement de disque sur des machines virtuelles Windows IaaS existantes ou en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Désactiver le chiffrement de disque sur des machines virtuelles Windows IaaS existantes ou en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Activer le chiffrement de disque sur une machine virtuelle Linux IaaS existante ou en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
  - [Désactiver le chiffrement sur une machine virtuelle Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - La désactivation du chiffrement est autorisée seulement sur les volumes de données pour les machines virtuelles Linux.  

### <a name="encrypt-or-decrypt-vm-scale-sets"></a>Chiffrer ou déchiffrer des groupes identiques de machines virtuelles

- [Activer le chiffrement de disque sur un groupe de machines virtuelles identiques Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Activer le chiffrement de disque sur un groupe de machines virtuelles identiques Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Déployer un groupe de machines virtuelles identiques Linux avec un serveur de rebond et autoriser le chiffrement sur VMSS Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Déployer un groupe de machines virtuelles identiques Windows avec un serveur de rebond et autoriser le chiffrement sur VMSS Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Désactiver le chiffrement de disque sur un groupe de machines virtuelles identiques Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Désactiver le chiffrement de disque sur un groupe de machines virtuelles identiques Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Chiffrer ou déchiffrer des machines virtuelles avec une application Azure AD (version précédente) 
 
- [Activer le chiffrement de disque sur des machines virtuelles Windows IaaS existantes ou en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Activer le chiffrement de disque sur une machine virtuelle Linux IaaS existante ou en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Désactiver le chiffrement de disque sur une machine virtuelle IaaS Windows en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Désactiver le chiffrement sur une machine virtuelle Linux en cours d’exécution](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - La désactivation du chiffrement est autorisée seulement sur les volumes de données pour les machines virtuelles Linux. 

- [Activer le chiffrement de disque sur une nouvelle machine virtuelle IaaS Windows à partir de la Place de marché](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Ce modèle crée une machine virtuelle Windows chiffrée qui utilise l’image de la galerie Windows Server 2012.

- [Créer une machine virtuelle avec disque managé IaaS Windows chiffré à partir d’une image de la galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Ce modèle crée une machine virtuelle Windows chiffrée avec des disques managés en utilisant l’image de la galerie Windows Server 2012.

- [Créer un disque managé chiffré à partir d’un disque dur virtuel/objet blob de stockage préchiffré](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Crée un disque managé chiffré fourni par un disque dur virtuel préchiffré et ses paramètres de chiffrement correspondants

- [Activer le chiffrement du disque sur une machine virtuelle Windows en cours d’exécution avec une empreinte de certificat client Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a> Préparer un disque dur virtuel Windows préchiffré
Les sections qui suivent sont nécessaires pour préparer un disque dur virtuel Windows déjà chiffré qui sera déployé comme disque dur virtuel chiffré dans Azure IaaS. Utilisez ces informations pour préparer et démarrer une nouvelle machine virtuelle Windows (disque dur virtuel) sur Azure Site Recovery ou Azure. Pour plus d’informations sur la préparation et le chargement d’un disque dur virtuel, consultez [Charger un disque dur virtuel généralisé et l’utiliser pour créer des machines virtuelles dans Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Mettre à jour la stratégie de groupe pour permettre la protection autre que par module de plateforme sécurisée pour la protection du système d’exploitation
Configurez le paramètre de stratégie de groupe BitLocker **Chiffrement de lecteur BitLocker** qui se trouve sous **Stratégie de l’ordinateur local** > **Configuration ordinateur** > **Modèles d’administration** > **Composants Windows**. Remplacez ce paramètre par **Lecteurs du système d’exploitation** > **Exiger une authentification supplémentaire au démarrage** > **Autoriser BitLocker sans un module de plateforme sécurisée compatible** comme indiqué dans la figure ci-dessous :

![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installer les composants de fonctionnalité BitLocker
Pour Windows Server 2012 ou version ultérieure, utilisez la commande suivante :

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Pour Windows Server 2008 R2, utilisez la commande suivante :

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Préparer le volume du système d’exploitation pour BitLocker à l’aide de `bdehdcfg`
Pour compresser la partition du système d’exploitation et préparer la machine pour BitLocker, exécutez si nécessaire [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) :

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Protéger le volume du système d’exploitation à l’aide de BitLocker
Utilisez la commande [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) pour activer le chiffrement sur le volume de démarrage à l’aide d’un protecteur de clé externe. Placez également la clé externe (fichier .bek) sur le disque ou le volume externe. Le chiffrement sera activé sur le volume système/de démarrage au prochain redémarrage.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Préparez la machine virtuelle avec un disque dur virtuel de données/de ressources distinct pour obtenir la clé externe à l’aide de BitLocker.

## <a name="bkmk_LinuxRunning"></a> Chiffrement d’un lecteur du système d’exploitation sur une machine virtuelle Linux en cours d’exécution

### <a name="prerequisites-for-os-disk-encryption"></a>Configuration requise pour le chiffrement du lecteur du système d’exploitation

* La machine virtuelle doit utiliser une distribution compatible avec le chiffrement de disque du système d’exploitation, comme indiqué dans le [Forum aux questions (FAQ) Azure Disk Encryption](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). 
* La machine virtuelle doit être créée à partir de l’image Marketplace dans Azure Resource Manager.
* Machine virtuelle Azure au moins 4 Go de RAM (la taille recommandée est de 7 Go).
* (Pour RHEL et CentOS) Désactivez SELinux. Pour désactiver SELinux, consultez la rubrique « 4.4.2. Désactivation de SELinux » dans le [Guide d’utilisation et d’administration SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) sur la machine virtuelle.
* Après avoir désactivé SELinux, redémarrez au moins une fois la machine virtuelle.

### <a name="steps"></a>Étapes
1. Créez une machine virtuelle en utilisant l’une des distributions spécifiées précédemment.

   Pour CentOS 7.2, le chiffrement du lecteur du système d’exploitation est pris en charge via une image spécifique. Pour utiliser cette image, spécifiez « 7.2n » en tant que référence lorsque vous créez la machine virtuelle :

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configurez la machine virtuelle selon vos besoins. Si vous souhaitez chiffrer tous les lecteurs (de données et du système d’exploitation), les lecteurs de données doivent être spécifiés et montables à partir de /etc/fstab.

   > [!NOTE]
   > Utilisez UUID =... pour définir les lecteurs de données dans /etc/fstab au lieu de spécifier le nom de l’appareil de traitement par blocs (par exemple, /dev/sdb1). L’ordre des lecteurs sur la machine virtuelle est modifié au cours du chiffrement. Si votre machine virtuelle s’appuie sur un ordre spécifique d’appareils de traitement par blocs, leur montage échouera après le chiffrement.

3. Déconnectez-vous des sessions SSH.

4. Pour chiffrer le système d’exploitation, spécifiez **All**  ou **OS** pour VolumeType quand vous activez le chiffrement.

   > [!NOTE]
   > Tous les processus d’espace utilisateur qui ne s’exécutent pas en tant que services `systemd` doivent être arrêtés avec un `SIGKILL`. Redémarrez la machine virtuelle. Lorsque vous activez le chiffrement du lecteur du système d’exploitation sur une machine virtuelle en cours d’exécution, prévoyez un temps d’arrêt de la machine virtuelle.

5. Surveillez régulièrement la progression du chiffrement à l’aide des instructions fournies dans la [section suivante](#monitoring-os-encryption-progress).

6. Une fois que Get-AzVmDiskEncryptionStatus indique « VMRestartPending », redémarrez votre machine virtuelle en vous connectant à ce dernier ou à l’aide du portail, de PowerShell ou CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Avant le redémarrage, nous vous recommandons d’enregistrer les [diagnostics de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) de la machine virtuelle.

## <a name="monitoring-os-encryption-progress"></a>Surveillance de la progression du chiffrement du système d’exploitation
Il existe trois façons de surveiller la progression du chiffrement du système d’exploitation :

* Utilisez l’applet de commande `Get-AzVmDiskEncryptionStatus` et examinez le champ ProgressMessage :
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Dès que la machine virtuelle passe à l’état « Le chiffrement du lecteur du système d’exploitation a démarré », l’opération prend entre 40 et 50 minutes sur une machine virtuelle avec un stockage Premium.

  En raison de [l’erreur #388](https://github.com/Azure/WALinuxAgent/issues/388) dans WALinuxAgent, `OsVolumeEncrypted` et `DataVolumesEncrypted` apparaissent comme `Unknown` dans certaines distributions. Ce problème est résolu automatiquement dans WALinuxAgent version 2.1.5 et ultérieure. Si `Unknown` s’affiche dans la sortie, vous pouvez vérifier l’état du chiffrement du disque en utilisant l’Explorateur de ressources Azure.

  Accédez à [l’Explorateur de ressources Azure](https://resources.azure.com/), puis développez cette hiérarchie dans le panneau de sélection de gauche :

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  Faites défiler InstanceView pour afficher l’état de chiffrement de vos lecteurs.

  ![Vue d’instance de machine virtuelle](./media/azure-security-disk-encryption/vm-instanceview.png)

* Recherchez les [diagnostics de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Les messages de l’extension ADE doivent être précédés du préfixe `[AzureDiskEncryption]`.

* Connectez-vous à la machine virtuelle via SSH et obtenez le journal des extensions à partir de :

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Nous vous déconseillons de vous connecter à la machine virtuelle lorsque le chiffrement du système d’exploitation est en cours. Copiez les journaux uniquement lorsque les deux autres méthodes ont échoué.

## <a name="bkmk_preLinux"></a> Préparer un disque dur virtuel Linux préchiffré
La préparation des disques durs virtuels préchiffrés peut varier en fonction de la distribution. Des exemples sur la préparation de [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE) et [CentOS 7](#bkmk_CentOS) sont disponibles. 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Configurez le chiffrement lors de l’installation de la distribution en procédant comme suit :

1. Sélectionnez **Configure encrypted volumes** (Configurer les volumes chiffrés) lors du partitionnement des disques.

   ![Configuration Ubuntu 16.04 - Configurer les volumes chiffrés](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Créez un lecteur de démarrage séparé qui ne doit pas être chiffré. Chiffrez votre lecteur racine.

   ![Configuration Ubuntu 16.04 - Sélectionner les appareils à chiffrer](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Indiquez une phrase secrète. Il s’agit de la phrase secrète que vous avez chargée dans le coffre de clés.

   ![Configuration Ubuntu 16.04 - Fournir la phrase secrète](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Terminez le partitionnement.

   ![Configuration Ubuntu 16.04 - Terminer le partitionnement](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Lorsque vous démarrez la machine virtuelle et devez fournir une phrase secrète, utilisez la phrase secrète que vous avez fournie à l’étape 3.

   ![Configuration Ubuntu 16.04 - Fournir la phrase secrète au démarrage](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Préparez la machine virtuelle au chargement dans Azure en suivant [ces instructions](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

Configurez le chiffrement pour l’utiliser dans Azure en effectuant les étapes suivantes :

1. Créez un fichier sous /usr/local/sbin/azure_crypt_key.sh, avec le contenu du script ci-dessous. Prêtez une attention particulière à KeyFileName, car il s’agit du nom de fichier de phrase secrète utilisé par Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Modifiez la configuration du chiffrement dans */etc/crypttab*. Il doit se présenter comme suit :
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Si vous modifiez *azure_crypt_key.sh* dans Windows et que vous le copiez sur Linux, exécutez `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Ajoutez des autorisations exécutables au script :
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Éditez */etc/initramfs-tools/modules* en ajoutant des lignes :
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Exécutez `update-initramfs -u -k all` pour mettre à jour l’initramfs afin de mettre en vigueur le `keyscript`.

7. Vous pouvez maintenant annuler l’approvisionnement de la machine virtuelle.

   ![Configuration d’Ubuntu 16.04 - update-initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Passez à l’étape suivante et chargez votre disque dur virtuel dans Azure.

### <a name="bkmk_openSUSE"></a> openSUSE 13.2
Pour configurer le chiffrement lors de l’installation de la distribution, effectuez les étapes suivantes :
1. Lorsque vous partitionnez les disques, sélectionnez **Chiffrer le groupe de volumes**, puis entrez un mot de passe. Il s’agit du mot de passe que vous allez charger dans votre coffre de clés.

   ![Configuration d’openSUSE 13.2 - Chiffrer un groupe de volumes](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Démarrez la machine virtuelle à l’aide de votre mot de passe.

   ![Configuration openSUSE 13.2 - Fournir la phrase secrète au démarrage](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Préparez la machine virtuelle pour le chargement dans Azure en suivant les instructions de la rubrique [Préparation d’une machine virtuelle SLES ou openSUSE pour Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

Pour configurer le chiffrement afin de l’utiliser dans Azure, effectuez les étapes suivantes :
1. Modifiez le fichier /etc/dracut.conf et ajoutez la ligne suivante :
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Mettez ces lignes en commentaire à la fin du fichier /usr/lib/dracut/modules.d/90crypt/module-setup.sh :
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Ajoutez la ligne suivante au début du fichier /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh :
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Puis, remplacez toutes les occurrences de :
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   to:
   ```bash
    if [ 1 ]; then
   ```
4. Modifiez /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et ajoutez-le à « # Open LUKS device » :

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Exécutez `/usr/sbin/dracut -f -v` pour mettre à jour le fichier initrd.

6. Vous pouvez maintenant déprovisionner la machine virtuelle et charger votre disque dur virtuel dans Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
Pour configurer le chiffrement lors de l’installation de la distribution, effectuez les étapes suivantes :
1. Sélectionnez **Chiffrer mes données** lors du partitionnement des disques.

   ![Configuration CentOS 7 - Destination de l’installation](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Vérifiez que **Chiffrer** est sélectionné pour la partition racine.

   ![Configuration CentOS 7 - Sélectionner le chiffrement pour la partition racine](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Indiquez une phrase secrète. Il s’agit de la phrase secrète que vous allez charger dans votre coffre de clés.

   ![Configuration CentOS 7 - Fournir la phrase secrète](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Lorsque vous démarrez la machine virtuelle et devez fournir une phrase secrète, utilisez la phrase secrète que vous avez fournie à l’étape 3.

   ![Configuration CentOS 7 - Entrer la phrase secrète lors du démarrage](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Préparez la machine virtuelle pour le chargement dans Azure en suivant les instructions « CentOS 7.0+ » dans la rubrique [Préparation d’une machine virtuelle CentOS pour Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

6. Vous pouvez maintenant déprovisionner la machine virtuelle et charger votre disque dur virtuel dans Azure.

Pour configurer le chiffrement afin de l’utiliser dans Azure, effectuez les étapes suivantes :

1. Modifiez le fichier /etc/dracut.conf et ajoutez la ligne suivante :
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Mettez ces lignes en commentaire à la fin du fichier /usr/lib/dracut/modules.d/90crypt/module-setup.sh :
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Ajoutez la ligne suivante au début du fichier /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh :
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Puis, remplacez toutes les occurrences de :
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   to
   ```bash
    if [ 1 ]; then
   ```
4. Modifiez /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et ajoutez ce qui suit après « # Open LUKS device » :
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Exécutez « /usr/sbin/dracut -f -v » pour mettre à jour le fichier initrd.

![Configuration CentOS 7 - Exécuter /usr/sbin/dracut -f -v](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Charger un disque dur virtuel chiffré dans un compte de stockage Azure
Une fois le chiffrement BitLocker ou DM-Crypt activé, le disque dur virtuel chiffré local doit être chargé sur votre compte de stockage.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Charger le secret pour la machine virtuelle préchiffrée dans votre coffre de clés
Lors du chiffrement avec une application Azure AD (version précédente), la clé secrète de chiffrement de disque obtenue précédemment doit être chargée en tant que clé secrète dans votre coffre de clés. Le chiffrement de disque et les autorisations du coffre de clés doivent être activés pour votre client Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Secret de chiffrement de disque non chiffré avec une clé KEK
Pour configurer la clé secrète dans votre coffre de clés, utilisez [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Si vous disposez d’une machine virtuelle Windows, le fichier bek est encodé sous forme de chaîne en base64, puis téléchargé dans le coffre de clés à l’aide de l’applet de commande `Set-AzKeyVaultSecret`. Pour Linux, la phrase secrète est encodée sous forme de chaîne en base64, puis téléchargée dans le coffre de clés. Assurez-vous également que les balises suivantes sont définies lors de la création de la clé secrète dans le coffre de clés.

#### <a name="windows-bek-file"></a>Fichier Windows BEK
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Utilisez `$secretUrl` à l’étape suivante pour [attacher le lecteur du système d’exploitation sans utiliser de clé de chiffrement à clé](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Secret de chiffrement de disque chiffré avec une clé KEK
Avant de télécharger la clé secrète dans le coffre de clés, vous pouvez éventuellement la chiffrer à l’aide d’une clé de chiffrement à clé. Utilisez [l’API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de retour à la ligne pour chiffrer d’abord la clé secrète à l’aide de la clé de chiffrement à clé. La sortie de cette opération de retour à la ligne est une chaîne d’URL encodée en base64 que vous pouvez ensuite charger comme clé secrète à l’aide de l’applet de commande [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret).

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Utilisez `$KeyEncryptionKey` et `$secretUrl` à l’étape suivante pour [attacher le lecteur du système d’exploitation à l’aide de la clé de chiffrement à clé](#bkmk_URLKEK).

##  <a name="bkmk_SecretURL"></a> Spécifier une URL de secret quand vous attachez un disque de système d’exploitation

###  <a name="bkmk_URLnoKEK"></a>Sans utiliser de clé KEK
Lorsque vous attachez le lecteur du système d’exploitation, exécutez la commande `$secretUrl`. L’URL a été générée dans la section « La clé secrète de chiffrement de disque non chiffrée avec une clé de chiffrement à clé ».
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Avec une clé KEK
Lorsque vous attachez le lecteur du système d’exploitation, exécutez les commandes `$KeyEncryptionKey` et `$secretUrl`. L’URL a été générée dans la section « Secret de chiffrement de disque chiffré avec une clé KEK ».
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
