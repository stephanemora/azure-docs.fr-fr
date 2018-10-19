---
title: Prérequis d’Azure Disk Encryption avec Azure AD App (version précédente) | Microsoft Docs
description: Cet article décrit les prérequis pour l’utilisation de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/10/2018
ms.openlocfilehash: 6d08dbe1976363be414597401d7a4efbae82c9b4
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498434"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Prérequis d’Azure Disk Encryption (version précédente)

**La nouvelle version d’Azure Disk Encryption vous évite de fournir un paramètre d’application Azure AD pour activer le chiffrement de disque de machine virtuelle. Avec la nouvelle version, vous n’êtes plus obligé de fournir les informations d’identification Azure AD lors de l’étape d’activation du chiffrement. Avec la nouvelle version, toutes les nouvelles machines virtuelles doivent être chiffrées sans les paramètres d’application Azure AD. Pour obtenir des instructions sur l’activation du chiffrement de disque de machine virtuelle avec la nouvelle version, consultez [Prérequis d’Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md). Les machines virtuelles déjà chiffrées avec les paramètres d’application Azure AD sont toujours prises en charge et doivent continuer à être gérées avec la syntaxe AAD.**

 Cet article décrit les éléments qui doivent être en place avant toute utilisation d’Azure Disk Encryption. Parallèlement aux prérequis généraux, Azure Disk Encryption est intégré à [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) et utilise une application Azure AD pour assurer l’authentification afin de gérer les clés de chiffrement dans le coffre de clés. Vous pouvez également utiliser [Azure PowerShell](/powershell/azure/overview) ou [l’interface de ligne de commande Azure (Azure CLI)](/cli/azure/) pour installer ou configurer la solution Key Vault et l’application Azure AD.

Avant d’activer Azure Disk Encryption sur les machines virtuelles IaaS Azure pour les scénarios pris en charge dans l’article de [présentation d’Azure Disk Encryption](azure-security-disk-encryption-overview.md), assurez-vous que tous les prérequis sont respectés. 

> [!NOTE]
> Certaines recommandations peuvent entraîner une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul débouchant sur des coûts de licence ou d’abonnement supplémentaires. Vous devez disposer d’un abonnement Azure actif valide pour créer des ressources dans Azure dans les régions prises en charge.


## <a name="bkmk_OSs"></a> Systèmes d’exploitation pris en charge
Azure Disk Encryption est pris en charge sur les systèmes d’exploitation suivants :

- Versions de Windows Server : Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016.
    - Pour Windows Server 2008 R2, .NET Framework 4.5 doit être installé avant l’activation du chiffrement dans Azure. Installez-le à partir de Windows Update avec la mise à jour facultative Microsoft .NET Framework 4.5.2 pour systèmes Windows Server 2008 R2 x64 ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Versions du client Windows : client Windows 8 et client Windows 10.
- Azure Disk Encryption est pris en charge uniquement sur les versions et les distributions de serveur Linux basées sur Azure Gallery. Pour obtenir la liste des versions actuellement prises en charge, reportez-vous à l’article [Forum aux questions (FAQ) Azure Disk Encryption](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- Azure Disk Encryption requiert que votre coffre de clés et vos machines virtuelles se trouvent dans la même région et le même abonnement Azure. La configuration des ressources dans des régions distinctes provoque l’échec de l’activation de la fonctionnalité Azure Disk Encryption.

## <a name="bkmk_LinuxPrereq"></a> Prérequis supplémentaires pour les machines virtuelles Iaas Linux 

- Azure Disk Encryption pour Linux exige 7 Go de RAM sur la machine virtuelle pour activer le chiffrement du lecteur du système d’exploitation sur les [images prises en charge](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Une fois que le processus de chiffrement du disque du système d’exploitation est terminé, il est possible de configurer la machine virtuelle pour qu’elle s’exécute avec moins de mémoire.
- Avant d’activer le chiffrement, vous devez répertorier correctement les disques de données à chiffrer dans /etc/fstab. Utilisez un nom d’appareil de traitement par blocs persistant pour cette entrée, car les noms d’appareil au format « /dev/sdX » ne restent pas nécessairement associés au même disque entre les redémarrages, en particulier après une opération de chiffrement. Pour plus d’informations sur ce comportement, consultez l’article [Résoudre les problèmes liés aux modifications des noms de périphérique de machine virtuelle Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md).
- Vérifiez que les paramètres /etc/fstab sont correctement configurés pour le montage. Pour configurer ces paramètres, exécutez la commande mount -a, ou redémarrez la machine virtuelle et déclenchez le remontage de cette façon. Une fois cette opération effectuée, vérifiez la sortie de la commande lsblk pour vous assurer que le lecteur souhaité est toujours monté. 
    - Si le fichier /etc/fstab ne monte pas correctement le lecteur avant l’activation du chiffrement, Azure Disk Encryption ne pourra pas procéder au montage du lecteur.
    - Le processus Azure Disk Encryption déplace les informations du fichier /etc/fstab vers son propre fichier de configuration dans le cadre de l’opération de chiffrement. Par conséquent, ne soyez pas surpris que l’entrée ne figure plus dans le fichier /etc/fstab après le chiffrement du lecteur de données.
    -  Après le redémarrage, le montage des disques nouvellement chiffrés par le processus Azure Disk Encryption nécessite un certain temps. Les disques ne sont donc pas disponibles juste après un redémarrage. Le processus a besoin de temps pour démarrer, déverrouiller, puis monter les lecteurs chiffrés avant que ces derniers ne deviennent accessibles à d’autres processus. Ce processus peut prendre plusieurs minutes après le redémarrage en fonction des caractéristiques du système.

Vous trouverez un exemple des commandes permettant de monter les disques de données et de créer les entrées /etc/fstab requises aux [lignes 197 à 205 de ce fichier de script](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205). 


## <a name="bkmk_GPO"></a> Mise en réseau et stratégie de groupe

**Pour l’activation de la fonctionnalité Azure Disk Encryption, les machines virtuelles IaaS doivent répondre aux exigences de configuration de point de terminaison de réseau suivantes :**
  - Pour obtenir un jeton afin de se connecter à votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Azure Active Directory \[Login.microsoftonline.com\].
  - Pour écrire les clés de chiffrement dans votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Key Vault.
  - La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison de stockage Azure qui héberge le référentiel d’extensions Azure et au compte de stockage Azure qui héberge les fichiers de disque dur virtuel.
  -  Si votre stratégie de sécurité limite l’accès à Internet à partir des machines virtuelles Azure, vous pouvez résoudre l’URI ci-dessus et configurer une règle spécifique pour autoriser les connexions sortantes vers les adresses IP. Pour plus d’informations, consultez l’article [Azure Key Vault derrière un pare-feu](../key-vault/key-vault-access-behind-firewall.md).    


**Stratégie de groupe :**
 - La solution Azure Disk Encryption utilise le protecteur de clé externe BitLocker pour les machines virtuelles IaaS Windows. Pour les machines virtuelles jointes à un domaine, n’envoyez (push) pas de stratégies de groupe qui appliquent des protecteurs de Module de plateforme sécurisée (TPM). Pour en savoir plus sur la stratégie de groupe pour « Autoriser BitLocker sans module de plateforme sécurisée compatible », consultez la rubrique [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup) (Référence de stratégie de groupe BitLocker).

-  La stratégie Bitlocker sur les machines virtuelles jointes à un domaine avec une stratégie de groupe personnalisée doit inclure le paramètre suivant : [Configurer le stockage par les utilisateurs des informations de récupération BitLocker -> Autoriser une clé de récupération de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). En cas d’incompatibilité des paramètres de stratégie de groupe personnalisée pour Bitlocker, Azure Disk Encryption échoue. Sur les machines dont le paramètre de stratégie était incorrect, il peut être nécessaire d’appliquer la nouvelle stratégie, de forcer la mise à jour de cette dernière (gpupdate.exe /force), puis de procéder à un redémarrage.  


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) fournit un ensemble d’applets de commande qui utilise le modèle [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour gérer vos ressources Azure. Vous pouvez l’ouvrir dans votre navigateur avec [Azure Cloud Shell](../cloud-shell/overview.md), ou vous pouvez l’installer sur votre ordinateur local à l’aide des instructions ci-après pour l’utiliser dans une session PowerShell. Si vous l’avez déjà installé localement, veillez à utiliser la dernière version du Kit de développement logiciel (SDK) Azure PowerShell pour configurer Azure Disk Encryption. Téléchargez la dernière version [d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Installez Azure PowerShell afin de l’utiliser sur votre ordinateur local (facultatif) : 
1. Suivez les instructions accessibles par le biais des liens correspondant à votre système d’exploitation, puis poursuivez en exécutant la procédure ci-après.      
    - [Installez et configurez Azure PowerShell pour Windows](/powershell/azure/install-azurerm-ps). 
        - Installez PowerShellGet, Azure PowerShell, puis chargez le module AzureRM. 
    - [Installez et configurez Azure PowerShell sur macOS et Linux](/powershell/azure/install-azurermps-maclinux).
        -  Installez PowerShell Core, Azure PowerShell pour .NET Core, puis chargez le module Az.
2. Installez le [module Azure Active Directory PowerShell](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Vérifiez les versions installées des modules.
      ```powershell
      Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Connectez-vous à Azure à l’aide de l’applet de commande [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).
     
     ```powershell
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Connectez-vous à Azure AD avec [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Si nécessaire, consultez les articles [Prise en main de Microsoft Azure PowerShell](/powershell/azure/get-started-azureps) et [Azure AD](/powershell/module/azuread).

## <a name="bkmk_CLI"></a> Azure CLI

[Azure CLI 2.0](/cli/azure) est un outil en ligne de commande pour la gestion des ressources Azure. L’interface CLI est conçue pour interroger les données de manière flexible, pour prendre en charge les opérations de longue durée en tant que processus non bloquants et pour simplifier l’écriture de scripts. Vous pouvez l’ouvrir dans le navigateur avec [Azure Cloud Shell](/cloud-shell/overview.md), ou vous pouvez l’installer sur votre ordinateur local et l’utiliser dans une session PowerShell.

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

4. Si nécessaire, consultez l’article [Bien démarrer avec Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Flux de travail requis pour Key Vault et l’application Azure AD

Si vous êtes déjà familiarisé avec les prérequis Key Vault et Azure AD pour Azure Disk Encryption, vous pouvez utiliser le [script PowerShell des prérequis d’Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Pour plus d’informations sur l’utilisation du script des prérequis, consultez le [Guide de démarrage rapide du chiffrement d’une machine virtuelle](quick-encrypt-vm-powershell.md) et [l’Annexe Azure Disk Encryption](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Création d’un coffre de clés 
2. Configurez une application Azure AD et le principal de service.
3. Définissez la stratégie d’accès au coffre de clés pour l’application Azure AD.
4. Définissez les stratégies d’accès avancé au coffre de clés.
 
## <a name="bkmk_KeyVault"></a> Créer un coffre de clés 
Azure Disk Encryption est intégré à [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) pour vous permettre de contrôler et gérer les clés et les secrets de chiffrement de disque dans votre abonnement de coffre de clés. Vous pouvez créer un coffre de clés ou utiliser un coffre existant pour Azure Disk Encryption. Pour plus d’informations sur les coffres de clés, consultez les articles [Prise en main du coffre de clés Azure](../key-vault/key-vault-get-started.md) et [Sécuriser votre coffre de clés](../key-vault/key-vault-secure-your-key-vault.md). Vous pouvez utiliser un modèle Resource Manager, Azure PowerShell ou Azure CLI pour créer un coffre de clés. 


>[!WARNING]
>Pour vous offrir l’assurance que les secrets de chiffrement ne franchissent pas les limites régionales, Azure Disk Encryption exige que le coffre de clés se trouve dans la même région que les machines virtuelles. Créez et utilisez un coffre de clés situé dans la même région que la machine virtuelle à chiffrer. 


### <a name="bkmk_KVPSH"></a> Créer un coffre de clés avec PowerShell

Vous pouvez créer un coffre de clés avec Azure PowerShell à l’aide de la cmdlet [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault). Pour découvrir les cmdlets supplémentaires pour Key Vault, consultez l’article [AzureRM.KeyVault](/powershell/module/azurerm.keyvault/). 

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Le cas échéant, créez un groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup).  Pour obtenir la liste des emplacements des centres de données, utilisez [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocationn). 
     
     ```azurepowershell-interactive
     # Get-AzureRmLocation 
     New-AzureRmResourceGroup –Name 'MySecureRG' –Location 'East US'
     ```

3. Créez un coffre de clés avec [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault).
    
      ```azurepowershell-interactive
     New-AzureRmKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -Location 'East US'
     ```

4. Notez les données **Nom du coffre**, **Nom du groupe de ressources**, **ID de ressource**, **URI du coffre** et **ID d’objet** qui sont renvoyées à des fins d’utilisation ultérieure dans le cadre du chiffrement des données. 


### <a name="bkmk_KVCLI"></a> Créer un coffre de clés avec Azure CLI
Vous pouvez gérer votre coffre de clés avec Azure CLI à l’aide des commandes [az keyvault](/cli/azure/keyvault#commands). Pour créer un coffre de clés, utilisez la commande [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Le cas échéant, créez un groupe de ressources avec la commande [az group create](/cli/azure/groupt#az-group-create). Pour obtenir la liste des emplacements, utilisez la commande [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MySecureRG" -l "East US"
     ```

3. Créez un coffre de clés avec la commande [az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MySecureRG" --location "East US"
     ```

4. Notez les données **Nom du coffre**, **Nom du groupe de ressources**, **ID de ressource**, **URI du coffre** et **ID d’objet** qui sont renvoyées à des fins d’utilisation ultérieure. 

### <a name="bkmk_KVRM"></a> Créer un coffre de clés avec un modèle Resource Manager

Vous pouvez créer un coffre de clés à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déploiement sur Azure**.
2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, le nom du coffre de clés, l’ID d’objet, les conditions juridiques et le contrat, puis cliquez sur **Acheter**. 


## <a name="bkmk_ADapp"></a> Configurer une application Azure AD et le principal de service 
Lorsque le chiffrement doit être activé sur une machine virtuelle en cours d’exécution dans Azure, Azure Disk Encryption génère et écrit les clés de chiffrement dans votre coffre de clés. La gestion des clés de chiffrement dans votre coffre de clés nécessite l’authentification Azure AD. Créez une application Azure AD à cet effet. Pour l’authentification, vous pouvez utiliser soit l’authentification par clé secrète client, soit [l’authentification Azure AD par certificat client](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Configurer une application Azure AD et un principal de service avec Azure PowerShell 
Pour exécuter les commandes suivantes, obtenez et utilisez le [module Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Pour créer une application Azure AD, utilisez la cmdlet PowerShell [New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication). Vous pouvez définir MyApplicationHomePage et MyApplicationUri sur les valeurs de votre choix.

     ```azurepowershell-interactive
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzureRmADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId est l’ID de client Azure AD, et $aadClientSecret est la clé secrète client que vous utiliserez par la suite pour activer Azure Disk Encryption. Prenez soin de bien sauvegarder la clé secrète de client Azure AD. L’exécution de l’élément `$azureAdApplication.ApplicationId` vous présentera l’ID d’application.


### <a name="bkmk_ADappCLI"></a> Configurer une application Azure AD et un principal de service avec Azure CLI

Vous pouvez gérer vos principaux de service avec Azure CLI à l’aide des commandes [az ad sp](/cli/azure/ad/sp). Pour plus d’informations, consultez l’article [Créer un principal du service Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Créez un principal de service.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  L’élément appId renvoyé est l’ID de client Azure AD utilisé dans d’autres commandes. Il s’agit également du nom de principal de service (SPN) que vous utiliserez pour la commande az keyvault set-policy. Le mot de passe est la clé secrète client que vous utiliserez par la suite pour activer Azure Disk Encryption. Prenez soin de bien sauvegarder la clé secrète de client Azure AD.
 
### <a name="bkmk_ADappRM"></a> Configurer une application Azure AD et un principal de service par le biais du Portail Azure
Pour créer une application Azure AD, exécutez la procédure de l’article [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](../azure-resource-manager/resource-group-create-service-principal-portal.md). Chaque étape répertoriée ci-après vous permet d’accéder directement à la section de l’article appropriée. 

1. [Vérifiez les autorisations requises](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
2. [Créez une application Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). 
     - Lorsque vous créez l’application, vous pouvez utiliser le nom et l’URL de connexion de votre choix.
3. [Obtenez l’ID d’application et la clé d’authentification](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key). 
     - La clé d’authentification correspond à la clé secrète client et est utilisée comme élément AadClientSecret pour Set-AzureRmVMDiskEncryptionExtension. 
        - La clé d’authentification est utilisée par l’application en tant qu’information d’identification pour la connexion à Azure AD. Dans le Portail Azure, ce secret est appelé clés, mais n’a aucun rapport avec les coffres de clés. Sécurisez ce secret de manière appropriée. 
     - L’ID d’application sera utilisé par la suite en tant qu’élément AadClientId pour Set-AzureRmVMDiskEncryptionExtension et en tant qu’élément ServicePrincipalName pour Set-AzureRmKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Définir la stratégie d’accès au coffre de clés pour l’application Azure AD
Pour écrire des secrets de chiffrement dans un coffre de clés spécifié, Azure Disk Encryption a besoin de l’ID client et de la clé secrète client de l’application Azure Active Directory qui dispose des autorisations pour écrire des secrets dans le coffre de clés. 

> [!NOTE]
> Azure Disk Encryption requiert de configurer les stratégies d’accès suivantes sur votre application cliente Azure AD : autorisations _WrapKey_ et _Set_.

### <a name="bkmk_KVAPPSH"></a> Définir la stratégie d’accès au coffre de clés pour l’application Azure AD avec Azure PowerShell
Votre application Azure AD a besoin d’autorisations d’accès aux clés ou aux clés secrètes dans le coffre. Exécutez la cmdlet [Set-AzureKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) pour accorder des autorisations à l’application, en utilisant l’ID client (qui a été généré quand l’application a été enregistrée) comme valeur du paramètre _–ServicePrincipalName_. Pour en savoir plus, consultez le billet de blog [Azure Key Vault – Étape par étape](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Définissez la stratégie d’accès au coffre de clés pour l’application AD avec PowerShell.

     ```azurepowershell-interactive
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $rgname = 'MySecureRG'
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
     ```

### <a name="bkmk_KVAPCLI"></a> Définir la stratégie d’accès au coffre de clés pour l’application Azure AD avec Azure CLI
Utilisez la commande [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault.md#az-keyvault-set-policy) pour définir la stratégie d’accès. Pour plus d’informations, consultez l’article [Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI) 2.0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. Si nécessaire, [connectez-vous à votre abonnement Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Fournissez le principal de service que vous avez créé par le biais de l’accès à Azure CLI pour obtenir les secrets et inclure les clés dans un wrapper avec la commande suivante :
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Définir la stratégie d’accès au coffre de clés pour l’application Azure AD avec le portail

1. Ouvrez le groupe de ressources avec votre coffre de clés.
2. Sélectionnez votre coffre de clés, accédez à **Stratégies d’accès**, puis cliquez sur **Ajouter nouveau**.
3. Sous **Sélectionner le principal**, recherchez l’application Azure AD que vous avez créée, puis sélectionnez-la. 
4. Pour **Autorisations de clé**, cochez la case en regard de **Inclure la clé** sous **Opérations de chiffrement**.
5. Pour **Autorisations du secret**, cochez la case en regard de **Définir** sous **Opérations de gestion des secrets**.
6. Cliquez sur **OK** pour enregistrer la stratégie d’accès. 

![Opérations de chiffrement Azure Key Vault - Inclure la clé](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Autorisations du secret Azure Key Vault - Définir ](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a>Définissez les stratégies d'accès avancé du coffre de clés
La plateforme Azure doit avoir accès aux clés et aux clés secrètes de chiffrement dans votre coffre de clés afin de les mettre à disposition de la machine virtuelle pour lancer et déchiffrer les volumes. Activez le chiffrement de disque sur le coffre de clés ; dans le cas contraire, les déploiements échoueront.  

### <a name="bkmk_KVperPSH"></a> Définir les stratégies d’accès avancé au coffre de clés avec Azure PowerShell
 Utilisez la cmdlet PowerShell de coffre de clés [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) afin d’activer le chiffrement de disque pour le coffre de clés.

  - **Activer Key Vault pour le chiffrement de disque :** EnabledForDiskEncryption est requis pour Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDiskEncryption
     ```

  - **Activer Key Vault pour le déploiement, si nécessaire :** autorise le fournisseur de ressources Microsoft.Compute à récupérer des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans le cadre de la création de ressources, par exemple lors de la création d’une machine virtuelle.

     ```azurepowershell-interactive
      Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDeployment
     ```

  - **Activer Key Vault pour le déploiement d’un modèle, si nécessaire :** autorise Azure Resource Manager à obtenir des secrets à partir de ce coffre de clés lorsque ce dernier est référencé dans un déploiement de modèle.

     ```azurepowershell-interactive             
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForTemplateDeployment`
     ```

### <a name="bkmk_KVperCLI"></a> Définir les stratégies d’accès avancé au coffre de clés avec Azure CLI
Utilisez la commande [az keyvault update](/cli/azure/keyvault#az-keyvault-update) afin d’activer le chiffrement de disque pour le coffre de clés. 

 - **Activer Key Vault pour le chiffrement de disque :** Enabled-for-disk-encryption est requis. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-disk-encryption "true"
     ```  

 - **Activer Key Vault pour le déploiement, si nécessaire :** autorise Machines Virtuelles à récupérer les certificats stockés en tant que secrets dans le coffre.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-deployment "true"
     ``` 

 - **Activer Key Vault pour le déploiement d’un modèle, si nécessaire :** autorise Resource Manager à récupérer des secrets dans le coffre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Définir les stratégies d’accès avancé au coffre de clés par le biais du Portail Azure

1. Sélectionnez votre coffre de clés, accédez à **Stratégies d’accès**, puis sélectionnez **Cliquez ici pour afficher les stratégies d’accès avancé**.
2. Cochez la case **Activer l’accès à Azure Disk Encryption pour chiffrer des volumes**.
3. Sélectionnez **Activer l’accès aux machines virtuelles Azure pour le déploiement** et/ou **Activer l’accès à Azure Resource Manager pour le déploiement de modèles** si nécessaire. 
4. Cliquez sur **Enregistrer**.

![Stratégies d’accès avancé au coffre de clés Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Configurer une clé de chiffrement à clé (facultatif)
Si vous souhaitez utiliser une clé de chiffrement à clé pour renforcer la protection des clés de chiffrement, ajoutez une clé de chiffrement à clé à votre coffre de clés. Utilisez la cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) pour créer une clé de chiffrement à clé dans le coffre de clés. Vous pouvez également importer une clé de chiffrement à clé à partir de votre module de sécurité matériel de gestion des clés locales. Pour plus d’informations, consultez la [documentation concernant Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Quand une clé de chiffrement principale est spécifiée, Azure Disk Encryption utilise cette clé pour wrapper les secrets de chiffrement avant d’écrire dans Key Vault. 

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
     # Fill in 'MyLocation', 'MySecureRG', and 'MySecureVault' with your values.
     # Use Get-AzureRmLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzureRmResourceGroup
     
     $Loc = 'MyLocation';
     $rgname = 'MySecureRG';
     $KeyVaultName = 'MySecureVault'; 
     New-AzureRmResourceGroup –Name $rgname –Location $Loc;
     New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc;
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $KeyVaultResourceId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname;
     
 #Step 4: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Authentification par certificat (facultatif)
Si vous souhaitez utiliser l’authentification par certificat, vous pouvez charger un certificat dans votre coffre de clés et le déployer sur le client. Avant d’utiliser le script PowerShell, vous devez connaître les prérequis pour Azure Disk Encryption afin de comprendre les étapes figurant dans le script. L’exemple de script peut nécessiter certaines modifications pour votre environnement.

     
 ```powershell

 # Fill in "MySecureRG", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Authentification par certificat et clé de chiffrement à clé (facultatif)

Si vous souhaitez utiliser l’authentification par certificat et inclure la clé de chiffrement dans un wrapper avec une clé de chiffrement à clé, vous pouvez utiliser le script ci-après en guise d’exemple. Avant d’utiliser le script PowerShell, vous devez connaître tous les prérequis précédents pour Azure Disk Encryption afin de comprendre les étapes figurant dans le script. L’exemple de script peut nécessiter certaines modifications pour votre environnement.

> [!IMPORTANT]
> L’authentification par certificat Azure AD n’est actuellement pas prise en charge sur les machines virtuelles Linux.



     
 ```powershell
# Fill in 'MySecureRG', 'MySecureVault', and 'MyLocation' (if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Activer Azure Disk Encryption pour les machines virtuelles IaaS Linux](azure-security-disk-encryption-linux-aad.md)
