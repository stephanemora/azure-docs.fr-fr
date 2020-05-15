---
title: Activer l’authentification Active Directory sur SMB pour Azure Files
description: Découvrez comment activer l’authentification basée sur l’identité sur SMB pour les partages de fichiers Azure via Active Directory. Vos machines virtuelles Windows jointes à un domaine peuvent alors accéder aux partages de fichiers Azure en utilisant des informations d’identification AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: febb796a47b9f5e78906d513c115b62b35c7c7d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196502"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Activer l’authentification Active Directory Domain Services locale sur SMB pour les partages de fichiers Azure

[Azure Files](storage-files-introduction.md) prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) via deux types de services de domaine : Azure Active Directory Domain Services (Azure AD DS) et Active Directory Domain Services en local (AD DS) (préversion). Cet article traite de la prise en charge récemment introduite (préversion) de l’utilisation du service de domaine Active Directory pour l’authentification auprès des partages de fichiers Azure. Si vous souhaitez activer l’authentification Azure AD DS (en disponibilité générale) pour les partages de fichiers Azure, reportez-vous à [notre article sur le sujet](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Les partages de fichiers Azure prennent uniquement en charge l’authentification par rapport à un service de domaine, Azure Active Directory Domain Services (Azure AD DS) ou Active Directory Domain Services en local (AD DS). 
>
> Les identités AD DS utilisées pour l’authentification auprès des partages de fichiers Azure doivent être synchronisées avec Azure AD. La synchronisation de hachage de mot de passe est facultative. 
> 
> L’authentification AD DS en local ne prend pas en charge l’authentification à l’aide des comptes d’ordinateur créés dans AD DS. 
> 
> L’authentification AD DS en local ne peut être prise en charge que sur une forêt AD dans laquelle le compte de stockage est inscrit. Vous pouvez uniquement accéder aux partages de fichiers Azure avec les informations d’identification AD DS à partir d’une forêt unique par défaut. Si vous avez besoin d’accéder à votre partage de fichiers Azure à partir d’une autre forêt, assurez-vous d’avoir configuré l’approbation de forêt appropriée. Pour plus d’informations, consultez le [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control).  
> 
> L’authentification AD DS pour l’accès SMB et la persistance des listes de contrôle d’accès (ACL) n’est pas prise en charge sur les partages de fichiers Azure managés par Azure File Sync.
>
> Azure Files prend en charge l’authentification Kerberos avec AD et le chiffrement RC4-HMAC. Le chiffrement Kerberos AES n’est pas encore pris en charge.

Lorsque vous activez AD DS pour les partages de fichiers Azure sur SMB, vos machines jointes à AD DS peuvent monter des partages de fichiers Azure à l’aide de vos informations d’identification AD existantes. Cette capacité peut être activée avec un environnement AD DS hébergé sur des machines locales ou hébergé dans Azure.

Les identités utilisées pour accéder aux partages de fichiers Azure doivent être synchronisées avec Azure AD pour appliquer les autorisations de fichiers au niveau du partage via le modèle de [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md). Les [DACL de style Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) sur des fichiers/répertoires reportés provenant de serveurs de fichiers existants seront conservées et appliquées. Cette fonctionnalité offre une intégration fluide à votre environnement AD DS d’entreprise. Lorsque vous remplacez des serveurs de fichiers locaux par des partages de fichiers Azure, les utilisateurs existants peuvent accéder aux partages de fichiers Azure à partir de leurs clients actuels à l’aide d’une expérience d’authentification unique, sans modification des informations d’identification utilisés.  

> [!NOTE]
> Afin de vous aider à configurer l’authentification AD Azure Files pour certains cas d’usage courants, nous avons publié [deux vidéos](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) avec des instructions pas à pas :
> - Remplacement des serveurs de fichiers locaux par Azure Files (y compris l’installation sur un lien privé pour les fichiers et l’authentification AD)
> - Utilisation d’Azure Files comme conteneur de profil pour Windows Virtual Desktop (y compris l’installation sur l’authentification AD et la configuration FsLogix)

## <a name="prerequisites"></a>Prérequis 

Avant d’activer l’authentification AD DS pour des partages de fichiers Azure, assurez-vous que les conditions préalables suivantes sont remplies : 

- Sélectionnez ou créez votre environnement AD DS, puis [synchronisez-le à Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Vous pouvez activer la fonctionnalité sur un environnement AD DS local nouveau ou existant. Les identités utilisées pour l’accès doivent être synchronisées à Azure AD. Le locataire Azure AD et le partage de fichiers auquel vous accédez doivent être associés au même abonnement. 

    Pour configurer un environnement de domaine AD, reportez-vous à [Présentation d’Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Si vous n’avez pas synchronisé votre environnement AD avec votre environnement Azure AD, suivez les instructions de la [Feuille de route pour l’installation d’Azure AD Connect et d’Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-install-roadmap.md) pour configurer et installer Azure AD Connect. 

- Joignez le domaine d’un ordinateur local ou d’une machine virtuelle Azure à AD DS en local. 

    Pour accéder à un partage de fichiers à l’aide des informations d’identification AD à partir d’un ordinateur ou d’une machine virtuelle, le domaine de cet appareil doit être joint à AD DS. Pour plus d’informations sur la façon de joindre un domaine, reportez-vous à [Joindre un ordinateur à un domaine](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Sélectionnez ou créez un compte de stockage Azure.  Pour des performances optimales, nous vous recommandons de déployer le compte de stockage dans la même région que celle de la machine virtuelle à partir de laquelle vous voulez accéder au partage.

    Assurez-vous que le compte de stockage contenant vos partages de fichiers n’est pas déjà configuré pour l’authentification Azure AD DS. Si l’authentification Azure AD DS pour Azure Files est activée sur le compte de stockage, elle doit être désactivée avant de changer pour AD DS en local. Cela implique que les ACL existantes configurées dans l’environnement Azure AD DS devront être reconfigurées pour l’application adéquate des autorisations.
    
    Pour plus d’informations sur la création d’un partage de fichiers, consultez  [Créer un partage de fichiers dans Azure Files](storage-how-to-create-file-share.md).

- Vérifiez la connectivité en montant des partages de fichiers Azure à l’aide de votre clé de compte de stockage. 

    Pour vérifier que votre appareil et le partage de fichiers sont correctement configurés, essayez de [monter le partage de fichiers](storage-how-to-use-files-windows.md) à l’aide de votre clé de compte de stockage. Si vous rencontrez des problèmes lors de la connexion à Azure Files, consultez [l’outil de dépannage que nous avons publié pour les erreurs de montage Azure Files sur Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Nous fournissons également des [conseils](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) pour contourner des situations où le port 445 est bloqué. 

## <a name="regional-availability"></a>Disponibilité régionale

L’authentification Azure Files avec AD DS (préversion) est disponible dans [toutes les régions publiques et les régions Azure Gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Vue d’ensemble

Si vous envisagez d’activer des configurations de mise en réseau sur votre partage de fichiers, nous vous recommandons de tenir compte des [considérations pour la mise en réseau](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) et de terminer la configuration associée avant d’activer l’authentification AD DS.

L’activation de l’authentification AD DS pour vos partages de fichiers Azure vous permet de vous authentifier auprès de vos partages de fichiers Azure avec vos informations d’identification AD DS locales. Elle vous permet en outre de mieux gérer vos autorisations pour contrôler les accès avec précision. Pour ce faire, vous devez synchroniser les identités de l’AD DS local sur Azure AD avec Azure AD Connect. Vous contrôlez l’accès au niveau partage avec des identités synchronisées sur Azure AD tout en gérant l’accès au niveau fichier/partage avec les informations d’identification AD DS locales.

Suivez ensuite les étapes ci-dessous pour configurer Azure Files pour l’authentification AD DS : 

1. [Activer l’authentification AD DS Azure Files sur votre compte de stockage](#1-enable-ad-ds-authentication-for-your-account)

1. [Attribuer des autorisations d’accès pour un partage de fichiers à une identité Azure AD (utilisateur, groupe ou principal de service) synchronisée avec l’identité AD cible](#2-assign-access-permissions-to-an-identity)

1. [Configurer des ACL sur SMB pour les répertoires et les fichiers](#3-configure-ntfs-permissions-over-smb)
 
1. [Monter un partage de fichiers Azure sur une machine virtuelle jointe à votre AD DS](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [Mettre à jour le mot de passe de l’identité de votre compte de stockage dans AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Le diagramme ci-dessous illustre le workflow de bout en bout pour l’activation de l’authentification Azure AD sur SMB pour les partages de fichiers Azure. 

![Diagramme de workflow AD pour Files](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> L’authentification AD DS sur SMB pour les partages de fichiers Azure est prise en charge uniquement sur des ordinateurs ou des machines virtuelles s’exécutant sur des versions de système d’exploitation ultérieures à Windows 7 ou Windows Server 2008 R2. 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1\. Activer l’authentification AD DS pour votre compte 

Pour activer l’authentification AD DS sur SMB pour les partages de fichiers Azure, vous devez d’abord inscrire votre compte de stockage auprès d’AD DS, puis définir les propriétés de domaine requises sur le compte de stockage. Lorsque la fonctionnalité est activée sur le compte de stockage, elle s’applique à tous les partages de fichiers nouveaux et existants dans le compte. Téléchargez le module PowerShell AzFilesHybrid et utilisez la cmdlet `join-AzStorageAccountForAuth` pour activer la fonctionnalité. Vous trouverez la description détaillée du workflow de bout en bout dans le script dans cette section. 

> [!IMPORTANT]
> La cmdlet `Join-AzStorageAccountForAuth` apporte des modifications à votre environnement AD. Lisez l’explication suivante pour mieux comprendre ce qu’elle fait pour s’assurer que vous disposez des autorisations appropriées pour exécuter la commande et que les modifications appliquées s’alignent sur les stratégies de conformité et de sécurité. 

La cmdlet `Join-AzStorageAccountForAuth` effectue l’équivalent d’une jonction de domaine hors connexion pour le compte de stockage indiqué. Le script utilise la cmdlet pour créer un compte dans votre domaine AD, soit un [compte d’ordinateur](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (par défaut), soit un [compte d’ouverture de session du service](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Si vous choisissez de le faire manuellement, vous devez sélectionner le compte le mieux adapté à votre environnement.

Le compte AD DS créé par la cmdlet représente le compte de stockage dans le domaine AD. Si le compte AD DS est créé sous une unité d’organisation (UO) qui applique l’expiration du mot de passe, vous devez mettre à jour le mot de passe avant la durée de vie maximale du mot de passe. Le fait de ne pas mettre à jour le mot de passe du compte entraîne des échecs d’authentification lors de l’accès aux partages de fichiers Azure. Pour savoir comment mettre à jour le mot de passe, consultez [Mettre à jour le mot de passe du compte AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Vous pouvez utiliser le script suivant pour effectuer l’inscription et activer la fonctionnalité, ou, à défaut, vous pouvez effectuer manuellement les opérations que le script ferait. Ces opérations sont décrites dans la section qui suit le script. Vous n’avez pas besoin de faire les deux.

### <a name="11-script-prerequisites"></a>1.1 Composants requis pour le script
- [Télécharger et décompresser le module AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installez et exécutez le module dans un appareil dont le domaine est joint à AD DS en local avec des informations d’identification AD DS et qui dispose des autorisations nécessaires pour créer un compte d’ouverture de session du service ou un compte d’ordinateur dans l’instance AD cible.
-  Exécutez le script à l’aide des informations d’identification AD DS en local synchronisées à votre Azure AD. Les informations d’identification AD DS en local doivent avoir les autorisations de rôle Propriétaire du compte de stockage ou RBAC de contributeur.
- Assurez-vous que votre compte de stockage se trouve dans une [région prise en charge](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1.2. Joindre votre compte de stockage à un domaine
N’oubliez pas de remplacer les valeurs d’espace réservé par les vôtres dans les paramètres ci-dessous avant d’exécuter le script dans PowerShell.
> [!IMPORTANT]
> La cmdlet de jonction de domaine crée un compte AD pour représenter le compte de stockage (partage de fichiers) dans AD. Vous pouvez choisir de vous inscrire en tant que compte d’ordinateur ou compte de connexion au service. Pour plus d’informations, consultez la [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control). Pour les comptes d’ordinateur, la durée de vie du mot de passe par défaut est définie à 30 jours dans AD. De même, le compte de connexion au service peut avoir une durée de vie de mot de passe par défaut définie sur le domaine AD ou l’unité d’organisation (UO).
> Pour les deux types de comptes, nous vous recommandons vivement de vérifier le délai d’expiration du mot de passe défini dans votre environnement AD et de [mettre à jour le mot de passe de l’identité de votre compte de stockage dans AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) pour le compte AD ci-dessous, avant la durée de vie maximale du mot de passe. Vous pouvez [créer une unité d’organisation (UO) AD dans AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) et désactiver en conséquence la stratégie d’expiration de mot de passe pour les [comptes d’ordinateur](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) ou les comptes de connexion au service. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

La description suivante récapitule toutes les actions effectuées lorsque la cmdlet `Join-AzStorageAccountForAuth` est exécutée. Si vous préférez ne pas utiliser la commande, vous pouvez effectuer ces étapes manuellement :

> [!NOTE]
> Si vous avez déjà exécuté le script `Join-AzStorageAccountForAuth` ci-dessus, passez à la section suivante : « 1.3. Confirmer que la fonctionnalité est activée ». Vous n’avez pas besoin d’effectuer à nouveau les opérations ci-dessous.

#### <a name="a-checking-environment"></a>a. Vérification de l’environnement

Tout d’abord, le script vérifie votre environnement. En particulier, il vérifie si [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) est installé et si l’interpréteur de commandes est en cours d’exécution avec des privilèges d’administrateur. Il vérifie ensuite si le [module Az.Storage 1.11.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) est installé, et l’installe s’il ne l’est pas. Si ces vérifications réussissent, il vérifie ensuite votre instance AD DS pour déterminer si un [compte d’ordinateur](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (par défaut) ou un [compte d’ouverture de session du service](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) a déjà été créé avec SPN/UPN comme « cifs/votre-nom-de-compte-de-stockage-ici.file.core.windows.net ». Si le compte n’existe pas, il en crée un comme décrit dans la section b ci-dessous.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Création manuelle d’une identité représentant le compte de stockage dans votre AD

Pour créer ce compte manuellement, créez une clé Kerberos pour votre compte de stockage en utilisant `New-AzStorageAccountKey -KeyName kerb1`. Ensuite, utilisez cette clé Kerberos comme mot de passe de votre compte. Cette clé est utilisée uniquement lors de la configuration et ne peut pas être utilisée pour des opérations de contrôle ou de plan de données sur le compte de stockage.

Une fois que vous avez cette clé, créez un compte de service ou d’ordinateur sous votre UO. Utilisez la spécification suivante : SPN : « cifs/votre-nom-de-compte-de-stockage-ici.file.core.windows.net » Mot de passe : Clé Kerberos pour votre compte de stockage.

Si votre UO applique l’expiration du mot de passe, vous devez mettre à jour le mot de passe avant la durée de vie maximale du mot de passe pour éviter les échecs d’authentification lors de l’accès aux partages de fichiers Azure. Pour plus d’informations, consultez [Mettre à jour le mot de passe de l’identité de votre compte de stockage dans AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Conservez l’ID de sécurité du compte nouvellement créé, vous en aurez besoin pour l’étape suivante. L’identité que vous avez créée et qui représente le compte de stockage n’a pas besoin d’être synchronisée avec Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Activer la fonctionnalité sur votre compte de stockage

Le script active ensuite la fonctionnalité sur votre compte de stockage. Pour effectuer cette installation manuellement, fournissez quelques détails de configuration pour les propriétés de domaine dans la commande suivante, puis exécutez-la. L’ID de sécurité du compte de stockage requis dans la commande suivante est l’ID de sécurité de l’identité que vous avez créée dans AD DS dans la [section précédente](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3. Confirmer que la fonctionnalité est activée

Pour vérifier si la fonctionnalité est activée sur votre compte de stockage, vous pouvez utiliser le script suivant :

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Vous avez maintenant activé la fonctionnalité sur votre compte de stockage. Maintenant que la fonctionnalité est activée, vous devez effectuer d’autres étapes pour l’utiliser.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Vous avez maintenant activé l’authentification AD DS sur SMB et attribué un rôle personnalisé qui fournit l’accès à un partage de fichiers Azure avec une identité AD DS. Pour accorder à d’autres utilisateurs l’accès à votre partage de fichiers, suivez les instructions fournies dans les sections [Attribuer des autorisations d’accès](#2-assign-access-permissions-to-an-identity) pour utiliser une identité et [Configurer les autorisations NTFS sur SMB](#3-configure-ntfs-permissions-over-smb).

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5\. Mettre à jour le mot de passe de l’identité de votre compte de stockage dans AD DS

Si vous avez inscrit l’identité ou le compte AD DS représentant votre compte de stockage sous une unité d’organisation qui applique l’expiration du mot de passe, vous devez opérer la rotation du mot de passe avant la durée de vie maximale du mot de passe. Le fait de ne pas mettre à jour le mot de passe du compte AD DS entraîne des échecs d’authentification lors de l’accès aux partages de fichiers Azure.  

Pour déclencher la rotation des mots de passe, vous pouvez exécuter la commande `Update-AzStorageAccountADObjectPassword` à partir du module AzFilesHybrid. La cmdlet effectue des actions similaires à la rotation des clés du compte de stockage. Elle récupère la deuxième clé Kerberos du compte de stockage et l’utilise pour mettre à jour le mot de passe du compte inscrit dans AD DS. Ensuite, elle régénère la clé Kerberos cible du compte de stockage et met à jour le mot de passe du compte inscrit dans AD DS. Vous devez exécuter cette cmdlet dans un environnement joint à un domaine AD DS en local.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Files et l’utilisation d’AD sur SMB, consultez les ressources suivantes :

- [Vue d’ensemble de la prise en charge de l’authentification basée sur l’identité Azure Files pour l’accès SMB](storage-files-active-directory-overview.md)
- [FORUM AUX QUESTIONS](storage-files-faq.md)
