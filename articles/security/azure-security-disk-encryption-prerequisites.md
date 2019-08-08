---
title: Conditions requises - Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article décrit les prérequis pour l’utilisation de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: 423fad943190232d9e5e674b98b62f4f0dffb8ae
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728748"
---
# <a name="azure-disk-encryption-prerequisites"></a>Prérequis pour Azure Disk Encryption

Cet article décrit les éléments qui doivent être en place avant toute utilisation d’Azure Disk Encryption. Azure Disk Encryption est intégré à [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) pour la gestion des clés de chiffrement. Vous pouvez utiliser [Azure PowerShell](/powershell/azure/overview), [Azure CLI](/cli/azure/) ou le [portail Azure](https://portal.azure.com) pour configurer Azure Disk Encryption.

Avant d’activer Azure Disk Encryption sur les machines virtuelles IaaS Azure pour les scénarios pris en charge dans l’article de [présentation d’Azure Disk Encryption](azure-security-disk-encryption-overview.md), assurez-vous que tous les prérequis sont respectés. 

> [!WARNING]
> - Si vous avez déjà utilisé [Azure Disk Encryption avec une application Azure AD](azure-security-disk-encryption-prerequisites-aad.md) pour chiffrer cette machine virtuelle, vous devrez continuer à utiliser cette option. Vous ne pouvez pas utiliser [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) sur cette machine virtuelle chiffrée car un tel scénario n’est pas pris en charge. Autrement dit, l’utilisation d’une autre application que l’application AAD pour cette machine virtuelle n’est pas encore prise en charge.
> - Certaines recommandations peuvent entraîner une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul débouchant sur des coûts de licence ou d’abonnement supplémentaires. Vous devez disposer d’un abonnement Azure actif valide pour créer des ressources dans Azure dans les régions prises en charge.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Tailles des machines virtuelles prises en charge

Azure Disk Encryption est disponible sur les machines virtuelles qui disposent de la mémoire minimale requise comme indiqué ci-dessous :

| Machine virtuelle | Mémoire minimale requise |
|--|--|
| Machines virtuelles Windows | 2 Go |
| Machines virtuelles Linux lors du chiffrement des volumes de données uniquement| 2 Go |
| Machines virtuelles Linux lors du chiffrement des volumes de système d’exploitation et de données, et où l’utilisation du système de fichiers racine (/) est de 4 Go ou moins | 8 Go |
| Machines virtuelles Linux lors du chiffrement des volumes de système d’exploitation et de données, et où l’utilisation du système de fichiers racine (/) est supérieure à 4 Go | L’utilisation du système de fichiers racine * 2. Par exemple, une utilisation du système de fichiers racine de 16 Go nécessite au moins 32 Go de RAM |

Une fois que le processus de chiffrement du disque de système d’exploitation est terminé sur les machines virtuelles Linux, il est possible de configurer la machine virtuelle pour qu’elle s’exécute avec moins de mémoire. 

> [!NOTE]
> Le chiffrement de disque de système d’exploitation Linux n’est pas disponible pour les [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/index.yml).

Azure Disk Encryption est également disponible pour les machines virtuelles avec stockage premium. 

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

### <a name="windows"></a>Windows

- Client Windows : Windows 8 et ultérieur.
- Serveur Windows : Windows Server 2008 R2 et ultérieur.  
 
> [!NOTE]
> Windows Server 2008 R2 nécessite l’installation de .NET Framework 4.5 pour le chiffrement. Installez-le à partir de Windows Update avec la mise à jour facultative Microsoft .NET Framework 4.5.2 pour les systèmes Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core et Windows Server 2016 Core nécessitent l’installation du composant bdehdcfg sur la machine virtuelle pour le chiffrement.


### <a name="linux"></a>Linux 

Azure Disk Encryption est pris en charge sur un sous-ensemble de [distributions Linux approuvées par Azure](../virtual-machines/linux/endorsed-distros.md), qui est lui-même un sous-ensemble de toutes les distributions de serveur Linux possibles.

![Diagramme de Venn de distributions de serveur Linux prenant en charge Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Les distributions de serveur Linux qui ne sont pas approuvées par Azure ne prennent pas en charge Azure Disk Encryption et, sur celles qui sont approuvées, seules les distributions et versions suivantes prennent en charge Azure Disk Encryption :

| Distribution Linux | Version | Type de volume pris en charge pour le chiffrement|
| --- | --- |--- |
| Ubuntu | 18,04| Disque de système d’exploitation et de données |
| Ubuntu | 16.04| Disque de système d’exploitation et de données |
| Ubuntu | 14.04.5</br>[avec le noyau Azure mis à jour vers la version 4.15 ou ultérieure](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Disque de système d’exploitation et de données |
| RHEL | 7.6 | Disque de système d’exploitation et de données (voir la remarque ci-dessous) |
| RHEL | 7.5 | Disque de système d’exploitation et de données (voir la remarque ci-dessous) |
| RHEL | 7.4 | Disque de système d’exploitation et de données (voir la remarque ci-dessous) |
| RHEL | 7.3 | Disque de système d’exploitation et de données (voir la remarque ci-dessous) |
| RHEL | 7,2 | Disque de système d’exploitation et de données (voir la remarque ci-dessous) |
| RHEL | 6.8 | Disque de données (voir la remarque ci-dessous) |
| RHEL | 6.7 | Disque de données (voir la remarque ci-dessous) |
| CentOS | 7.6 | Disque de système d’exploitation et de données |
| CentOS | 7.5 | Disque de système d’exploitation et de données |
| CentOS | 7.4 | Disque de système d’exploitation et de données |
| CentOS | 7.3 | Disque de système d’exploitation et de données |
| CentOS | 7.2n | Disque de système d’exploitation et de données |
| CentOS | 6.8 | Disque de données |
| openSUSE | 42.3 | Disque de données |
| SLES | 12-SP4 | Disque de données |
| SLES | 12-SP3 | Disque de données |

> [!NOTE]
> La nouvelle implémentation d’ADE est prise en charge pour les disques de système d’exploitation et de données RHEL pour les images avec paiement à l’utilisation de RHEL7. ADE n’est actuellement pas pris en charge pour les images BYOS de RHEL. Consultez [Azure Disk Encryption pour Linux](azure-security-disk-encryption-linux.md) pour plus d’informations.

- Azure Disk Encryption requiert que votre coffre de clés et vos machines virtuelles se trouvent dans la même région et le même abonnement Azure. La configuration des ressources dans des régions distinctes provoque l’échec de l’activation de la fonctionnalité Azure Disk Encryption.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Prérequis supplémentaires pour les machines virtuelles IaaS Linux 

- Azure Disk Encryption nécessite la présence des modules dm-crypt et vfat sur le système. La suppression ou la désactivation de vfat de l’image par défaut empêchera le système de lire le volume de clés et d’obtenir la clé nécessaire pour déverrouiller les disques lors des redémarrages suivants. Les étapes de renforcement du système qui suppriment le module vfat du système ne sont pas compatibles avec Azure Disk Encryption. 
- Avant d’activer le chiffrement, vous devez répertorier correctement les disques de données à chiffrer dans /etc/fstab. Utilisez un nom d’appareil de traitement par blocs persistant pour cette entrée, car les noms d’appareil au format « /dev/sdX » ne restent pas nécessairement associés au même disque entre les redémarrages, en particulier après une opération de chiffrement. Pour plus d’informations sur ce comportement, consultez : [Résoudre les problèmes liés aux modifications des noms de périphérique de machine virtuelle Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Vérifiez que les paramètres /etc/fstab sont correctement configurés pour le montage. Pour configurer ces paramètres, exécutez la commande mount -a, ou redémarrez la machine virtuelle et déclenchez le remontage de cette façon. Une fois cette opération effectuée, consultez la sortie de la commande lsblk pour vérifier que le lecteur est toujours monté. 
  - Si le fichier /etc/fstab ne monte pas correctement le lecteur avant l’activation du chiffrement, Azure Disk Encryption ne pourra pas procéder au montage du lecteur.
  - Le processus Azure Disk Encryption déplace les informations du fichier /etc/fstab vers son propre fichier de configuration dans le cadre de l’opération de chiffrement. Par conséquent, ne soyez pas surpris que l’entrée ne figure plus dans le fichier /etc/fstab après le chiffrement du lecteur de données.
  - Avant de démarrer le chiffrement, veillez à arrêter tous les services et processus pouvant écrire sur des disques de données montés, et à les désactiver de sorte qu’ils ne redémarrent pas automatiquement après un redémarrage. Ils peuvent maintenir des fichiers ouverts sur ces partitions, empêchant ainsi la procédure de chiffrement de les remonter, d’où une défaillance du chiffrement. 
  - Après le redémarrage, le montage des disques nouvellement chiffrés par le processus Azure Disk Encryption nécessite un certain temps. Les disques ne sont donc pas disponibles juste après un redémarrage. Le processus a besoin de temps pour démarrer, déverrouiller, puis monter les lecteurs chiffrés avant que ces derniers ne deviennent accessibles à d’autres processus. Ce processus peut prendre plusieurs minutes après le redémarrage en fonction des caractéristiques du système.

Vous trouverez un exemple des commandes permettant de monter les disques de données et de créer les entrées /etc/fstab requises aux [lignes 244 à 248 de ce fichier de script](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Mise en réseau et stratégie de groupe

**Pour l’activation de la fonctionnalité Azure Disk Encryption, les machines virtuelles IaaS doivent répondre aux exigences de configuration de point de terminaison de réseau suivantes :**
  - Pour obtenir un jeton afin de se connecter à votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Azure Active Directory \[Login.microsoftonline.com\].
  - Pour écrire les clés de chiffrement dans votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Key Vault.
  - La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison de stockage Azure qui héberge le référentiel d’extensions Azure et au compte de stockage Azure qui héberge les fichiers de disque dur virtuel.
  -  Si votre stratégie de sécurité limite l’accès à Internet à partir des machines virtuelles Azure, vous pouvez résoudre l’URI ci-dessus et configurer une règle spécifique pour autoriser les connexions sortantes vers les adresses IP. Pour plus d’informations, consultez l’article [Azure Key Vault derrière un pare-feu](../key-vault/key-vault-access-behind-firewall.md).    


**Stratégie de groupe :**
 - La solution Azure Disk Encryption utilise le protecteur de clé externe BitLocker pour les machines virtuelles IaaS Windows. Pour les machines virtuelles jointes à un domaine, n’envoyez (push) pas de stratégies de groupe qui appliquent des protecteurs de Module de plateforme sécurisée (TPM). Pour en savoir plus sur la stratégie de groupe pour « Autoriser BitLocker sans module de plateforme sécurisée compatible », consultez la rubrique [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1) (Référence de stratégie de groupe BitLocker).

-  La stratégie BitLocker sur les machines virtuelles jointes à un domaine avec stratégie de groupe personnalisée doit inclure le paramètre suivant : [Configurer le stockage par les utilisateurs des informations de récupération BitLocker -> Autoriser une clé de récupération de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). En cas d'incompatibilité des paramètres de la stratégie de groupe personnalisée de BitLocker, Azure Disk Encryption échouera. Sur les machines dont le paramètre de stratégie était incorrect, il peut être nécessaire d’appliquer la nouvelle stratégie, de forcer la mise à jour de cette dernière (gpupdate.exe /force), puis de procéder à un redémarrage.

- Azure Disk Encryption échoue si la stratégie de groupe au niveau du domaine bloque l’algorithme AES-CBC, qui est utilisé par BitLocker.


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) fournit un ensemble d’applets de commande qui utilise le modèle [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour gérer vos ressources Azure. Vous pouvez l’ouvrir dans votre navigateur avec [Azure Cloud Shell](../cloud-shell/overview.md), ou vous pouvez l’installer sur votre ordinateur local à l’aide des instructions ci-après pour l’utiliser dans une session PowerShell. Si vous l’avez déjà installé localement, veillez à utiliser la dernière version du Kit de développement logiciel (SDK) Azure PowerShell pour configurer Azure Disk Encryption. Téléchargez la dernière version [d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Installez Azure PowerShell afin de l’utiliser sur votre ordinateur local (facultatif) : 
1. Suivez les instructions accessibles par le biais des liens correspondant à votre système d’exploitation, puis poursuivez en exécutant la procédure ci-après.      
   - [Installez et configurez Azure PowerShell](/powershell/azure/install-az-ps). 
     - Installez PowerShellGet, Azure PowerShell, puis chargez le module Az. 

2. Vérifiez les versions installées du module Az. Si nécessaire, [mettez à jour du module Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    Nous vous recommandons d’utiliser la dernière version du module Az.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Connectez-vous à Azure à l’aide de l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  Si nécessaire, consultez [Bien démarrer avec Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a> Installer Azure CLI afin de l’utiliser sur votre ordinateur local (facultatif)

[Azure CLI 2.0](/cli/azure) est un outil en ligne de commande pour la gestion des ressources Azure. L’interface CLI est conçue pour interroger les données de manière flexible, pour prendre en charge les opérations de longue durée en tant que processus non bloquants et pour simplifier l’écriture de scripts. Vous pouvez l’ouvrir dans le navigateur avec [Azure Cloud Shell](../cloud-shell/overview.md), ou vous pouvez l’installer sur votre ordinateur local et l’utiliser dans une session PowerShell.

1. [Installez Azure CLI](/cli/azure/install-azure-cli) afin de l’utiliser sur votre ordinateur local (facultatif) :

2. Vérifiez la version installée.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Connectez-vous à Azure avec la commande [az login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Si nécessaire, consultez [Bien démarrer avec Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 


## <a name="prerequisite-workflow-for-key-vault"></a>Flux de travail des prérequis pour Key Vault
Si vous êtes déjà familiarisé avec les prérequis Key Vault et Azure AD pour Azure Disk Encryption, vous pouvez utiliser le [script PowerShell des prérequis d’Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Pour plus d’informations sur l’utilisation du script des prérequis, consultez le [Guide de démarrage rapide du chiffrement d’une machine virtuelle](azure-disk-encryption-linux-powershell-quickstart.md) et l’[Annexe Azure Disk Encryption](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Si nécessaire, créez un groupe de ressources.
2. Création d’un coffre de clés 
3. Définissez les stratégies d’accès avancé au coffre de clés.

>[!WARNING]
>Avant de supprimer un coffre de clés, assurez-vous de n’avoir chiffré aucune machine virtuelle avec celui-ci. Pour protéger un coffre contre une suppression accidentelle, [activez la suppression réversible](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) et un [verrou de ressource](../azure-resource-manager/resource-group-lock-resources.md) sur le coffre. 
 
## <a name="bkmk_KeyVault"></a> Créer un coffre de clés 
Azure Disk Encryption est intégré à [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) pour vous permettre de contrôler et gérer les clés et les secrets de chiffrement de disque dans votre abonnement de coffre de clés. Vous pouvez créer un coffre de clés ou utiliser un coffre existant pour Azure Disk Encryption. Pour plus d’informations sur les coffres de clés, consultez les articles [Prise en main du coffre de clés Azure](../key-vault/key-vault-get-started.md) et [Sécuriser votre coffre de clés](../key-vault/key-vault-secure-your-key-vault.md). Vous pouvez utiliser un modèle Resource Manager, Azure PowerShell ou Azure CLI pour créer un coffre de clés. 


>[!WARNING]
>Pour vous offrir l’assurance que les secrets de chiffrement ne franchissent pas les limites régionales, Azure Disk Encryption exige que le coffre de clés se trouve dans la même région que les machines virtuelles. Créez et utilisez un coffre de clés situé dans la même région que la machine virtuelle à chiffrer. 


### <a name="bkmk_KVPSH"></a> Créer un coffre de clés avec PowerShell

Vous pouvez créer un coffre de clés avec Azure PowerShell à l’aide de l’applet de commande [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault). Pour découvrir les applets de commande supplémentaires pour Key Vault, consultez l’article [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Le cas échéant, créez un groupe de ressources avec la commande [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Pour obtenir la liste des emplacements des centres de données, utilisez [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Créez un coffre de clés avec [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault).
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Notez les données **Nom du coffre**, **Nom du groupe de ressources**, **ID de ressource**, **URI du coffre** et **ID d’objet** qui sont renvoyées à des fins d’utilisation ultérieure dans le cadre du chiffrement des données. 


### <a name="bkmk_KVCLI"></a> Créer un coffre de clés avec Azure CLI
Vous pouvez gérer votre coffre de clés avec Azure CLI à l’aide des commandes [az keyvault](/cli/azure/keyvault#commands). Pour créer un coffre de clés, utilisez la commande [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Le cas échéant, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Pour obtenir la liste des emplacements, utilisez la commande [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Créez un coffre de clés avec la commande [az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Notez les données **Nom du coffre**, **Nom du groupe de ressources**, **ID de ressource**, **URI du coffre** et **ID d’objet** qui sont renvoyées à des fins d’utilisation ultérieure. 

### <a name="bkmk_KVRM"></a> Créer un coffre de clés avec un modèle Resource Manager

Vous pouvez créer un coffre de clés à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déploiement sur Azure**.
2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, le nom du coffre de clés, l’ID d’objet, les conditions juridiques et le contrat, puis cliquez sur **Acheter**. 


## <a name="bkmk_KVper"></a>Définissez les stratégies d'accès avancé du coffre de clés
La plateforme Azure doit avoir accès aux clés et aux clés secrètes de chiffrement dans votre coffre de clés afin de les mettre à disposition de la machine virtuelle pour lancer et déchiffrer les volumes. Activez le chiffrement de disque sur le coffre de clés ; dans le cas contraire, les déploiements échoueront.  

### <a name="bkmk_KVperPSH"></a> Définir les stratégies d’accès avancé au coffre de clés avec Azure PowerShell
 Utilisez l’applet de commande PowerShell de coffre de clés [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) pour activer le chiffrement de disque pour le coffre de clés.

  - **Activer Key Vault pour le chiffrement de disque :** EnabledForDiskEncryption est requis pour Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Activer Key Vault pour le déploiement, si nécessaire :** autorise le fournisseur de ressources Microsoft.Compute à récupérer des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans le cadre de la création de ressources, par exemple lors de la création d’une machine virtuelle.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Activer Key Vault pour le déploiement d’un modèle, si nécessaire :** autorise Azure Resource Manager à obtenir des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans un déploiement de modèle.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Définir les stratégies d’accès avancé au coffre de clés avec Azure CLI
Utilisez la commande [az keyvault update](/cli/azure/keyvault#az-keyvault-update) afin d’activer le chiffrement de disque pour le coffre de clés. 

 - **Activer Key Vault pour le chiffrement de disque :** Enabled-for-disk-encryption est requis. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Activer Key Vault pour le déploiement, si nécessaire :** autorise le fournisseur de ressources Microsoft.Compute à récupérer des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans le cadre de la création de ressources, par exemple lors de la création d’une machine virtuelle.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Activer Key Vault pour le déploiement d’un modèle, si nécessaire :** autoriser Resource Manager à récupérer des secrets dans le coffre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Définir les stratégies d’accès avancé au coffre de clés par le biais du Portail Azure

1. Sélectionnez votre coffre de clés, accédez à **Stratégies d’accès**, puis sélectionnez **Cliquez ici pour afficher les stratégies d’accès avancé**.
2. Cochez la case **Activer l’accès à Azure Disk Encryption pour chiffrer des volumes**.
3. Sélectionnez **Activer l’accès aux machines virtuelles Azure pour le déploiement** et/ou **Activer l’accès à Azure Resource Manager pour le déploiement de modèles** si nécessaire. 
4. Cliquez sur **Enregistrer**.

    ![Stratégies d’accès avancé au coffre de clés Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Configurer une clé de chiffrement à clé (facultatif)
Si vous souhaitez utiliser une clé de chiffrement à clé pour renforcer la protection des clés de chiffrement, ajoutez une clé de chiffrement à clé à votre coffre de clés. Utilisez la cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) pour créer une clé de chiffrement à clé dans le coffre de clés. Vous pouvez également importer une clé de chiffrement à clé à partir de votre module de sécurité matériel de gestion des clés locales. Pour plus d’informations, consultez la [documentation concernant Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Quand une clé de chiffrement principale est spécifiée, Azure Disk Encryption utilise cette clé pour wrapper les secrets de chiffrement avant d’écrire dans Key Vault.

* Lors de la génération de clés, utilisez un type de clé RSA. Azure Disk Encryption ne prend pas encore en charge l’utilisation de clés à courbe elliptique.

* Les URL de clé secrète de coffre de clés et de clé de chiffrement à clé (KEK) doivent être des versions gérées. Azure met en vigueur cette restriction de gestion de version. Voici des exemples d’URL de clé secrète et de clé de chiffrement à clé valides :

  * Exemple d’URL de secret valide : *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemple d’URL KEK valide : *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption ne prend pas en charge l’intégration de numéros de port aux secrets de coffre de clés et aux URL KEK. Voici des exemples d’URL de coffre de clés valides et non valides :

  * URL de coffre de clés inacceptable : *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL de coffre de clés acceptable : *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Configurer une clé de chiffrement à clé avec Azure PowerShell 
Avant d’utiliser le script PowerShell, vous devez connaître les prérequis pour Azure Disk Encryption afin de comprendre les étapes figurant dans le script. L’exemple de script peut nécessiter certaines modifications pour votre environnement. Ce script crée tous les prérequis pour Azure Disk Encryption et chiffre une machine virtuelle IaaS existante en incluant la clé de chiffrement de disque dans un wrapper à l’aide d’une clé de chiffrement à clé. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour les machines virtuelles IaaS Linux](azure-security-disk-encryption-linux.md)

