---
title: Conditions requises - Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article décrit les prérequis pour l’utilisation de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: 1da35b55a458ad73689f51c49e73855fd33ee45f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117995"
---
# <a name="azure-disk-encryption-prerequisites"></a>Prérequis pour Azure Disk Encryption

Cet article décrit les éléments qui doivent être en place avant toute utilisation d’Azure Disk Encryption. Azure Disk Encryption est intégré à [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) pour la gestion des clés de chiffrement. Vous pouvez utiliser [Azure PowerShell](/powershell/azure/overview), [Azure CLI](/cli/azure/) ou le [portail Azure](https://portal.azure.com) pour configurer Azure Disk Encryption.

Avant d’activer Azure Disk Encryption sur les machines virtuelles IaaS Azure pour les scénarios pris en charge dans l’article de [présentation d’Azure Disk Encryption](azure-security-disk-encryption-overview.md), assurez-vous que tous les prérequis sont respectés. 

> [!WARNING]
> - Si vous avez déjà utilisé [Azure Disk Encryption avec une application Azure AD](azure-security-disk-encryption-prerequisites-aad.md) pour chiffrer cette machine virtuelle, vous devrez continuer à utiliser cette option. Vous ne pouvez pas utiliser [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) sur cette machine virtuelle chiffrée car un tel scénario n’est pas pris en charge. Autrement dit, l’utilisation d’une autre application que l’application AAD pour cette machine virtuelle n’est pas encore prise en charge.
> - Certaines recommandations peuvent entraîner une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul débouchant sur des coûts de licence ou d’abonnement supplémentaires. Vous devez disposer d’un abonnement Azure actif valide pour créer des ressources dans Azure dans les régions prises en charge.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_OSs"></a> Systèmes d’exploitation pris en charge
Azure Disk Encryption est pris en charge sur les systèmes d’exploitation suivants :

- Versions de Windows Server : Core de Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016, Windows Server 2012 R2 Server Core et Windows Server 2016 Server.
Pour Windows Server 2008 R2, .NET Framework 4.5 doit être installé avant l’activation du chiffrement dans Azure. Installez-le à partir de la mise à jour de Windows avec la mise à jour facultative Microsoft .NET Framework 4.5.2 pour systèmes x64 64 de Windows Server 2008 R2 (KB2901983).
- Windows Server 2012 R2 Core et Windows Server 2016 Core sont pris en charge par Azure Disk Encryption une fois que le composant bdehdcfg est installé sur la machine virtuelle.
- Versions des clients Windows : client Windows 8 et client Windows 10.
- Azure Disk Encryption est pris en charge uniquement sur les versions et les distributions de serveur Linux basées sur Azure Gallery. Pour obtenir la liste des versions actuellement prises en charge, reportez-vous à l’article [Forum aux questions (FAQ) Azure Disk Encryption](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Reportez-vous à la [distributions Linux approuvées sur Azure](../virtual-machines/linux/endorsed-distros.md) pour obtenir la liste d’images pris en charge par Microsoft et à la [Azure Disk Encryption prend-il en charge les distributions Linux que ?](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) dans le [Azure Forum aux questions sur le chiffrement de disque](azure-security-disk-encryption-faq.md) pour obtenir la liste des versions actuellement prises en charge basée sur les distributions approuvées image.
- Azure Disk Encryption requiert que votre coffre de clés et vos machines virtuelles se trouvent dans la même région et le même abonnement Azure. La configuration des ressources dans des régions distinctes provoque l’échec de l’activation de la fonctionnalité Azure Disk Encryption.

## <a name="bkmk_LinuxPrereq"></a> Conditions préalables supplémentaires pour les machines virtuelles IaaS Linux 

- Azure Disk Encryption pour Linux exige 7 Go de RAM sur la machine virtuelle pour activer le chiffrement du lecteur du système d’exploitation sur les [images prises en charge](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Une fois que le processus de chiffrement du disque du système d’exploitation est terminé, il est possible de configurer la machine virtuelle pour qu’elle s’exécute avec moins de mémoire.
- Azure Disk Encryption requiert le module vfat doit se trouver sur le système.  Suppression ou la désactivation de ce module à partir de l’image par défaut empêche le système d’être en mesure de lire le volume de clé et obtenir la clé nécessaire pour déverrouiller les disques sur les redémarrages suivants. Procédure de renforcement de système permettant de supprimer le module vfat à partir du système n’est pas compatibles avec Azure Disk Encryption. 
- Avant d’activer le chiffrement, vous devez répertorier correctement les disques de données à chiffrer dans /etc/fstab. Utilisez un nom d’appareil de traitement par blocs persistant pour cette entrée, car les noms d’appareil au format « /dev/sdX » ne restent pas nécessairement associés au même disque entre les redémarrages, en particulier après une opération de chiffrement. Pour plus d’informations sur ce comportement, consultez : [Résoudre les problèmes liés aux modifications des noms de périphérique de machine virtuelle Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Vérifiez que les paramètres /etc/fstab sont correctement configurés pour le montage. Pour configurer ces paramètres, exécutez la commande mount -a, ou redémarrez la machine virtuelle et déclenchez le remontage de cette façon. Une fois cette opération effectuée, consultez la sortie de la commande lsblk pour vérifier que le lecteur est toujours monté. 
  - Si le fichier /etc/fstab ne monte pas correctement le lecteur avant l’activation du chiffrement, Azure Disk Encryption ne pourra pas procéder au montage du lecteur.
  - Le processus Azure Disk Encryption déplace les informations du fichier /etc/fstab vers son propre fichier de configuration dans le cadre de l’opération de chiffrement. Par conséquent, ne soyez pas surpris que l’entrée ne figure plus dans le fichier /etc/fstab après le chiffrement du lecteur de données.
  -  Après le redémarrage, le montage des disques nouvellement chiffrés par le processus Azure Disk Encryption nécessite un certain temps. Les disques ne sont donc pas disponibles juste après un redémarrage. Le processus a besoin de temps pour démarrer, déverrouiller, puis monter les lecteurs chiffrés avant que ces derniers ne deviennent accessibles à d’autres processus. Ce processus peut prendre plusieurs minutes après le redémarrage en fonction des caractéristiques du système.

Vous trouverez un exemple des commandes permettant de monter les disques de données et de créer les entrées /etc/fstab requises aux [lignes 244 à 248 de ce fichier de script](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 


## <a name="bkmk_GPO"></a> Mise en réseau et stratégie de groupe

**Pour l’activation de la fonctionnalité Azure Disk Encryption, les machines virtuelles IaaS doivent répondre aux exigences de configuration de point de terminaison de réseau suivantes :**
  - Pour obtenir un jeton afin de se connecter à votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Azure Active Directory \[Login.microsoftonline.com\].
  - Pour écrire les clés de chiffrement dans votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Key Vault.
  - La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison de stockage Azure qui héberge le référentiel d’extensions Azure et au compte de stockage Azure qui héberge les fichiers de disque dur virtuel.
  -  Si votre stratégie de sécurité limite l’accès à Internet à partir des machines virtuelles Azure, vous pouvez résoudre l’URI ci-dessus et configurer une règle spécifique pour autoriser les connexions sortantes vers les adresses IP. Pour plus d’informations, consultez l’article [Azure Key Vault derrière un pare-feu](../key-vault/key-vault-access-behind-firewall.md).    


**Stratégie de groupe :**
 - La solution Azure Disk Encryption utilise le protecteur de clé externe BitLocker pour les machines virtuelles IaaS Windows. Pour les machines virtuelles jointes à un domaine, n’envoyez (push) pas de stratégies de groupe qui appliquent des protecteurs de Module de plateforme sécurisée (TPM). Pour en savoir plus sur la stratégie de groupe pour « Autoriser BitLocker sans module de plateforme sécurisée compatible », consultez la rubrique [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup) (Référence de stratégie de groupe BitLocker).

-  La stratégie BitLocker sur les machines virtuelles jointes à un domaine avec stratégie de groupe personnalisée doit inclure le paramètre suivant : [Configurer le stockage par les utilisateurs des informations de récupération BitLocker -> Autoriser une clé de récupération de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). En cas d'incompatibilité des paramètres de la stratégie de groupe personnalisée de BitLocker, Azure Disk Encryption échouera. Sur les machines dont le paramètre de stratégie était incorrect, il peut être nécessaire d’appliquer la nouvelle stratégie, de forcer la mise à jour de cette dernière (gpupdate.exe /force), puis de procéder à un redémarrage.

- Azure Disk Encryption échoue si la stratégie de groupe au niveau domaine bloque l’algorithme AES-CBC, qui est utilisé par Bitlocker.


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) fournit un ensemble d’applets de commande qui utilise le modèle [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour gérer vos ressources Azure. Vous pouvez l’ouvrir dans votre navigateur avec [Azure Cloud Shell](../cloud-shell/overview.md), ou vous pouvez l’installer sur votre ordinateur local à l’aide des instructions ci-après pour l’utiliser dans une session PowerShell. Si vous l’avez déjà installé localement, veillez à utiliser la dernière version du Kit de développement logiciel (SDK) Azure PowerShell pour configurer Azure Disk Encryption. Téléchargez la dernière version [d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Installez Azure PowerShell afin de l’utiliser sur votre ordinateur local (facultatif) : 
1. Suivez les instructions accessibles par le biais des liens correspondant à votre système d’exploitation, puis poursuivez en exécutant la procédure ci-après.      
   - [Installez et configurez Azure PowerShell](/powershell/azure/install-az-ps). 
     - Installer PowerShellGet, Azure PowerShell et charger le module Az. 

2. Vérifiez les versions installées du module Az. Si nécessaire, [mettez à jour du module Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    L’utilisation de la dernière version du module Az est recommandée.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Connectez-vous à Azure à l’aide du [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) applet de commande.
     
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
Si vous êtes déjà familiarisé avec les prérequis Key Vault et Azure AD pour Azure Disk Encryption, vous pouvez utiliser le [script PowerShell des prérequis d’Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Pour plus d’informations sur l’utilisation du script des prérequis, consultez le [Guide de démarrage rapide du chiffrement d’une machine virtuelle](quick-encrypt-vm-powershell.md) et l’[Annexe Azure Disk Encryption](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

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

Vous pouvez créer un coffre de clés avec Azure PowerShell à l’aide du [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) applet de commande. Pour les applets de commande supplémentaires pour Key Vault, consultez [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Créer un nouveau groupe de ressources, si nécessaire, avec [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Pour répertorier les emplacements des centres de données, utilisez [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Créer un nouveau coffre de clés à l’aide [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
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
 Utilisez l’applet de commande PowerShell coffre de clés [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) pour activer le chiffrement de disque pour le coffre de clés.

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
Si vous souhaitez utiliser une clé de chiffrement à clé pour renforcer la protection des clés de chiffrement, ajoutez une clé de chiffrement à clé à votre coffre de clés. Utilisez le [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) applet de commande pour créer une clé de chiffrement à clé dans le coffre de clés. Vous pouvez également importer une clé de chiffrement à clé à partir de votre module de sécurité matériel de gestion des clés locales. Pour plus d’informations, consultez la [documentation concernant Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Quand une clé de chiffrement principale est spécifiée, Azure Disk Encryption utilise cette clé pour wrapper les secrets de chiffrement avant d’écrire dans Key Vault.

* Lors de la génération de clés, utilisez un type de clé RSA. Azure Disk Encryption ne prend pas en charge à l’aide de clés de courbe elliptique.

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
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
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

