---
title: Activer l’authentification Active Directory sur SMB pour Azure Files
description: Découvrez comment activer l’authentification basée sur l’identité sur SMB pour les partages de fichiers Azure via Active Directory. Vos machines virtuelles Windows jointes à un domaine peuvent alors accéder aux partages de fichiers Azure en utilisant des informations d’identification AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2020
ms.author: rogarana
ms.openlocfilehash: 9bda3331bc27f387bd05ea0fcd29e7badf6d6a02
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651081"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Activer l’authentification Active Directory sur SMB pour les partages de fichiers Azure

[Azure Files](storage-files-introduction.md) prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) via deux types de services de domaine : Azure Active Directory Domain Services (Azure AD DS) [en disponibilité générale] et Active Directory (AD) [préversion]. Cet article traite de la prise en charge récemment introduite (préversion) de l’utilisation du service de domaine Active Directory pour l’authentification auprès des partages de fichiers Azure. Si vous souhaitez activer l’authentification Azure AD DS (en disponibilité générale) pour les partages de fichiers Azure, reportez-vous à [notre article sur le sujet](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Les partages de fichiers Azure prennent uniquement en charge l’authentification par rapport à un service de domaine, le service de domaine Azure Active Directory (Azure AD DS) ou Active Directory (AD). 
>
> Les identités AD utilisées pour l’authentification auprès des partages de fichiers Azure doivent être synchronisées avec Azure AD. La synchronisation de hachage de mot de passe est facultative. 
> 
> L’authentification AD ne prend pas en charge l’authentification à l’aide des comptes d’ordinateur créés dans AD. 
> 
> L’authentification AD ne peut être prise en charge que sur une forêt AD dans laquelle le compte de stockage est inscrit. Vous pouvez uniquement accéder aux partages de fichiers Azure avec les informations d’identification AD à partir d’une forêt AD unique par défaut. Si vous avez besoin d’accéder à votre partage de fichiers Azure à partir d’une autre forêt, assurez-vous d’avoir configuré l’approbation de forêt appropriée. Pour plus d’informations, consultez le [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control).  
> 
> L’authentification AD pour l’accès SMB et la persistance des listes de contrôle d’accès (ACL, access-control list) n’est pas prise en charge sur les partages de fichiers Azure managés par Azure File Sync.
>
> Azure Files prend en charge l’authentification Kerberos avec AD et le chiffrement RC4-HMAC. Le chiffrement Kerberos AES n’est pas encore pris en charge.

Lorsque vous activez AD pour les partages de fichiers Azure sur SMB, vos machines jointes à un domaine AD peuvent monter des partages de fichiers Azure à l’aide de vos informations d’identification AD existantes. Cette capacité peut être activée avec un environnement AD hébergé sur des machines locales ou hébergées dans Azure.

Les identités AD utilisées pour accéder aux partages de fichiers Azure doivent être synchronisées avec Azure AD pour appliquer les autorisations de fichiers au niveau du partage via le modèle standard de [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md). Les [DACL de style Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) sur des fichiers/répertoires reportés provenant de serveurs de fichiers existants seront conservées et appliquées. Cette fonctionnalité offre une intégration transparente à votre infrastructure de domaine AD d’entreprise. Lorsque vous remplacez des serveurs de fichiers locaux par des partages de fichiers Azure, les utilisateurs existants peuvent accéder aux partages de fichiers Azure à partir de leurs clients actuels à l’aide d’une expérience d’authentification unique, sans modification des informations d’identification utilisés.  
 
## <a name="prerequisites"></a>Prérequis 

Avant d’activer l’authentification AD pour des partages de fichiers Azure, assurez-vous que les conditions préalables suivantes sont remplies : 

- Sélectionnez ou créez votre environnement AD, puis synchronisez-le à Azure AD. 

    Vous pouvez activer la fonctionnalité sur un environnement AD nouveau ou existant. Les identités utilisées pour l’accès doivent être synchronisées à Azure AD. Le locataire Azure AD et le partage de fichiers auquel vous accédez doivent être associés au même abonnement. 

    Pour configurer un environnement de domaine AD, reportez-vous à [Présentation d’Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Si vous n’avez pas synchronisé votre AD à votre Azure AD, suivez les instructions décrites dans [Présentation de l’identité hybride avec Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) pour déterminer la méthode d’authentification de votre choix et l’option de configuration Azure AD Connect. 

- Joindre le domaine d’un ordinateur local ou d’une machine virtuelle Azure à AD (également appelée AD DS). 

    Pour accéder à un partage de fichiers à l’aide des informations d’identification AD à partir d’un ordinateur ou d’une machine virtuelle, le domaine de cet appareil doit être joint à AD. Pour plus d’informations sur la façon de joindre un domaine à AD, reportez-vous à [Joindre un ordinateur à un domaine](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Sélectionnez ou créez un compte de stockage Azure dans [une région prise en charge](#regional-availability). 

    Assurez-vous que le compte de stockage contenant vos partages de fichiers n’est pas déjà configuré pour l’authentification Azure AD DS. Si l’authentification Azure AD DS pour Azure Files est activée sur le compte de stockage, elle doit être désactivée avant de changer pour AD. Cela implique que les ACL existantes configurées dans l’environnement Azure AD DS devront être reconfigurées pour l’application adéquate des autorisations.
    
    Pour plus d’informations sur la création d’un partage de fichiers, consultez  [Créer un partage de fichiers dans Azure Files](storage-how-to-create-file-share.md).
    
    Pour des performances optimales, nous vous recommandons de déployer le compte de stockage dans la même région que celle de la machine virtuelle à partir de laquelle vous voulez accéder au partage. 

- Vérifiez la connectivité en montant des partages de fichiers Azure à l’aide de votre clé de compte de stockage. 

    Pour vérifier que votre appareil et le partage de fichiers sont configurés correctement, essayez de monter le partage de fichiers à l’aide de votre clé de compte de stockage. Pour plus d’informations, consultez  [Utiliser un partage de fichiers Azure avec Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilité régionale

L’authentification AD pour Azure Files est disponible dans [la plupart des régions publiques](https://azure.microsoft.com/global-infrastructure/regions/). Le sous-ensemble de régions dans lequel elle n’est pas encore disponible est comme suit :

- USA Ouest
- USA Ouest 2
- USA Est
- USA Est 2
- États-Unis - partie centrale méridionale
- Europe Ouest
- Europe Nord

## <a name="workflow-overview"></a>Vue d’ensemble du workflow

Avant d’activer l’authentification AD sur SMB pour les partages de fichiers Azure, nous vous recommandons de passer en revue les  [conditions préalables](#prerequisites) et de vous assurer que vous avez effectué toutes les étapes. Les conditions préalables confirment que vos environnements AD, Azure AD et Stockage Azure sont correctement configurés. 

Ensuite, accordez l’accès aux ressources Azure Files avec les informations d’identification AD : 

- Activez l’authentification AD pour Azure Files pour votre compte de stockage.  

- Attribuez des autorisations d’accès à un partage de fichiers à une identité Azure AD (utilisateur, groupe ou principal de service) qui est synchronisée à l’identité AD cible. 

- Configurez les ACL sur SMB pour les fichiers et les répertoires. 

- Montez un partage de fichiers Azure à partir d’une machine virtuelle jointe à un domaine AD. 

Le diagramme ci-dessous illustre le workflow de bout en bout pour l’activation de l’authentification Azure AD sur SMB pour les partages de fichiers Azure. 

![Diagramme de workflow AD pour Files](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> L’authentification AD sur SMB pour les partages de fichiers Azure est prise en charge uniquement sur des ordinateurs ou des machines virtuelles s’exécutant sur des versions de système d’exploitation ultérieures à Windows 7 ou Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Activer l’authentification AD pour votre compte 

Pour activer l’authentification AD sur SMB pour les partages de fichiers Azure, vous devez d’abord inscrire votre compte de stockage auprès d’AD, puis définir les propriétés de domaine requises sur le compte de stockage. Lorsque la fonctionnalité est activée sur le compte de stockage, elle s’applique à tous les partages de fichiers nouveaux et existants dans le compte. Utilisez `join-AzStorageAccountForAuth` pour activer la fonctionnalité. Vous trouverez la description détaillée du workflow de bout en bout dans la section ci-dessous. 

> [!IMPORTANT]
> La cmdlet `join-AzStorageAccountForAuth` apporte des modifications à votre environnement AD. Lisez l’explication suivante pour mieux comprendre ce qu’elle fait pour s’assurer que vous disposez des autorisations appropriées pour exécuter la commande et que les modifications appliquées s’alignent sur les stratégies de conformité et de sécurité. 

La cmdlet `join-AzStorageAccountForAuth` effectue l’équivalent d’une jonction de domaine hors connexion pour le compte de stockage indiqué. Elle créera un compte dans votre domaine AD, soit un [compte d’ordinateur](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory), soit un [compte d’ouverture de session du service](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Le compte AD créé représente le compte de stockage dans le domaine AD. Si le compte AD est créé sous une unité d’organisation (UO) AD qui applique l’expiration du mot de passe, vous devez mettre à jour le mot de passe avant la durée de vie maximale du mot de passe. Le fait de ne pas mettre à jour le mot de passe du compte AD entraîne des échecs d’authentification lors de l’accès aux partages de fichiers Azure. Pour savoir comment mettre à jour le mot de passe, consultez [Mettre à jour le mot de passe du compte AD](#update-ad-account-password).

Vous pouvez utiliser le script suivant pour effectuer l’inscription et activer la fonctionnalité, ou, à défaut, vous pouvez effectuer manuellement les opérations que le script ferait. Ces opérations sont décrites dans la section qui suit le script. Vous n’avez pas besoin de faire les deux.

### <a name="1-check-prerequisites"></a>1. Vérifier les conditions préalables
- [Télécharger et décompresser le module AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installez et exécutez le module dans un appareil dont le domaine est joint à AD par des informations d’identification AD et qui dispose des autorisations nécessaires pour créer un compte d’ouverture de session du service ou un compte d’ordinateur dans l’instance AD cible.
-  Exécutez le script à l’aide des informations d’identification AD synchronisées à votre Azure AD. Les informations d’identification AD doivent avoir les autorisations de rôle Propriétaire du compte de stockage ou RBAC contributeur.
- Assurez-vous que votre compte de stockage se trouve dans une [région prise en charge](#regional-availability).

### <a name="2-execute-ad-enablement-script"></a>2. Exécuter le script d’activation AD
N’oubliez pas de remplacer les valeurs d’espace réservé par les vôtres dans les paramètres ci-dessous avant d’exécuter le script dans PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```

La description suivante récapitule toutes les actions effectuées lorsque la cmdlet `join-AzStorageAccountForAuth` est exécutée. Si vous préférez ne pas utiliser la commande, vous pouvez effectuer ces étapes manuellement :

> [!NOTE]
> Si vous avez déjà exécuté le script join-AzStorageAccountForAuth ci-dessus, passez à la section suivante : « 3. Confirmer que la fonctionnalité est activée ». Vous n’avez pas besoin d’effectuer à nouveau les opérations ci-dessous.

#### <a name="a-checking-environment"></a>a. Vérification de l’environnement

Tout d’abord, le script vérifie votre environnement. En particulier, il vérifie si [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) est installé et si l’interpréteur de commandes est en cours d’exécution avec des privilèges d’administrateur. Il vérifie ensuite si le [module Az.Storage 1.11.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) est installé, et l’installe s’il ne l’est pas. Si ces vérifications réussissent, il vérifiera ensuite votre AD pour déterminer si un [compte d’ordinateur](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (par défaut) ou un [compte d’ouverture de session du service](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) a déjà été créé avec SPN/UPN comme « cifs/votre-nom-de-compte-de-stockage-ici.file.core.windows.net ». Si le compte n’existe pas, il en crée un comme décrit dans la section b ci-dessous.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Création manuelle d’une identité représentant le compte de stockage dans votre AD

Pour créer ce compte manuellement, créez une clé Kerberos pour votre compte de stockage en utilisant `New-AzStorageAccountKey -KeyName kerb1`. Ensuite, utilisez cette clé Kerberos comme mot de passe de votre compte. Cette clé est utilisée uniquement lors de la configuration et ne peut pas être utilisée pour des opérations de contrôle ou de plan de données sur le compte de stockage.

Une fois que vous avez cette clé, créez un compte de service ou d’ordinateur sous votre UO. Utilisez la spécification suivante : SPN : « cifs/votre-nom-de-compte-de-stockage-ici.file.core.windows.net » Mot de passe : Clé Kerberos pour votre compte de stockage.

Si votre UO applique l’expiration du mot de passe, vous devez mettre à jour le mot de passe avant la durée de vie maximale du mot de passe pour éviter les échecs d’authentification lors de l’accès aux partages de fichiers Azure. Pour plus d’informations, consultez [Mettre à jour le mot de passe du compte AD](#update-ad-account-password).

Conservez l’ID de sécurité du compte nouvellement créé, vous en aurez besoin pour l’étape suivante.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Activer la fonctionnalité sur votre compte de stockage

Le script active ensuite la fonctionnalité sur votre compte de stockage. Pour effectuer cette installation manuellement, fournissez quelques détails de configuration pour les propriétés de domaine dans la commande suivante, puis exécutez-la. L’ID de sécurité du compte de stockage requis dans la commande suivante est l’ID de sécurité de l’identité que vous avez créée dans AD (section b ci-dessus).

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Confirmer que la fonctionnalité est activée

Pour vérifier si la fonctionnalité est activée sur votre compte de stockage, vous pouvez utiliser le script suivant :

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Vous avez maintenant activé la fonctionnalité sur votre compte de stockage. Même si la fonctionnalité est activée, vous devez encore effectuer des actions supplémentaires pour pouvoir l’utiliser correctement.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Vous avez maintenant activé l’authentification AD sur SMB et attribué un rôle personnalisé qui fournit l’accès à un partage de fichiers Azure avec une identité AD. Pour accorder à d’autres utilisateurs l’accès à votre partage de fichiers, suivez les instructions fournies dans les sections [Attribuer des autorisations d’accès](#assign-access-permissions-to-an-identity) pour utiliser une identité et [Configurer les autorisations NTFS sur SMB](#configure-ntfs-permissions-over-smb).

## <a name="update-ad-account-password"></a>Mettre à jour le mot de passe du compte AD

Si vous avez inscrit l’identité ou le compte AD représentant votre compte de stockage sous une unité d’organisation qui applique l’expiration du mot de passe, vous devez opérer la rotation du mot de passe avant la durée de vie maximale du mot de passe. Le fait de ne pas mettre à jour le mot de passe du compte AD entraîne des échecs d’authentification lors de l’accès aux partages de fichiers Azure.  

Pour déclencher la rotation des mots de passe, vous pouvez exécuter la commande `Update-AzStorageAccountADObjectPassword` à partir du module AzFilesHybrid. La cmdlet effectue des actions similaires à la rotation des clés du compte de stockage. Elle récupère la deuxième clé Kerberos du compte de stockage et l’utilise pour mettre à jour le mot de passe du compte inscrit dans AD. Ensuite, elle régénère la clé Kerberos cible du compte de stockage et met à jour le mot de passe du compte inscrit dans AD. Vous devez exécuter cette cmdlet dans un environnement joint à un domaine AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Files et l’utilisation d’AD sur SMB, consultez les ressources suivantes :

- [Vue d’ensemble de la prise en charge de l’authentification basée sur l’identité Azure Files pour l’accès SMB](storage-files-active-directory-overview.md)
- [FORUM AUX QUESTIONS](storage-files-faq.md)
