---
title: Activer l’authentification Azure Active Directory sur SMB pour Azure Files (préversion) - Stockage Azure
description: Découvrez comment activer l’authentification basée sur l’identité sur SMB (Server Message Block) (préversion) pour Azure Files avec Azure Active Directory (Azure AD) Domain Services. Vos machines virtuelles Windows jointes à un domaine peuvent alors accéder aux partages de fichiers Azure en utilisant les informations d’identification Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696123"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Activer l’authentification des services de domaine Azure Active Directory sur SMB pour Azure Files (préversion)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Pour avoir une vue d’ensemble de l’authentification Azure AD sur SMB pour Azure Files, consultez [Vue d’ensemble de l’authentification Azure Active Directory sur SMB pour Azure Files (préversion)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Vue d’ensemble du workflow
Avant d’activer l’authentification Azure AD Domain Services sur SMB pour Azure Files, vérifiez que vos environnements Azure AD et Stockage Azure sont correctement configurés. Nous vous recommandons de passer en revue les [prérequis](#prerequisites) pour vérifier que vous avez effectué toutes les étapes nécessaires. 

Ensuite, accordez l’accès aux ressources Azure Files avec les informations d’identification Azure AD en suivant ces étapes : 

1. Activez l’authentification Azure AD Domain Services sur SMB pour votre compte de stockage afin d’inscrire le compte de stockage auprès du déploiement Azure AD Domain Services associé.
2. Attribuez des autorisations d’accès à un partage à une identité Azure AD (utilisateur, groupe ou principal de service).
3. Configurez les autorisations NTFS sur SMB pour les fichiers et les répertoires.
4. Montez un partage de fichiers Azure à partir d’une machine virtuelle jointe à un domaine.

Le diagramme ci-dessous illustre le workflow de bout en bout pour l’activation de l’authentification Azure AD Domain Services sur SMB pour Azure Files. 

![Diagramme montrant le workflow AD Azure sur SMB pour Azure Files](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Prérequis 

Avant d’activer Azure AD sur SMB pour Azure Files, assurez-vous de remplir les conditions préalables suivantes :

1.  **Sélectionner ou créer un locataire Azure AD.**

    Vous pouvez utiliser un locataire nouveau ou existant pour l’authentification Azure AD sur SMB. Le locataire et le partage de fichiers auquel vous voulez accéder doivent être associés au même abonnement.

    Pour créer un locataire Azure AD, vous pouvez [Ajouter un locataire Azure AD et un abonnement Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Si vous avez déjà un locataire Azure AD, mais que vous voulez en créer un pour l’utiliser avec Azure Files, consultez [Créer un locataire Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Activer Azure AD Domain Services sur le locataire Azure AD.**

    Pour prendre en charge l’authentification avec les informations d’identification Azure AD, vous devez activer Azure AD Domain Services pour votre locataire Azure AD. Si vous n’êtes pas l’administrateur du locataire Azure AD, contactez-le et suivez les instructions pas à pas pour [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/create-instance.md).

    Il faut généralement environ 15 minutes pour déployer Azure AD Domain Services. Vérifiez que l’état d’intégrité d’Azure AD Domain Services est **En cours d’exécution**, avec la synchronisation de hachage de mot de passe activée, avant de passer à l’étape suivante.

3.  **Joindre une machine virtuelle à Azure AD Domain Services.**

    Pour accéder à un partage de fichiers à l’aide des informations d’identification Azure AD à partir d’une machine virtuelle, cette machine virtuelle doit être jointe à Azure AD Domain Services. Pour plus d’informations sur la jonction de domaine d’une machine virtuelle, consultez [Joindre une machine virtuelle Windows Server à un domaine géré](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > L’authentification Azure AD Domain Services sur SMB avec Azure Files est prise en charge uniquement sur les machines virtuelles Azure s’exécutant sur des versions de système d’exploitation ultérieures à Windows 7 ou Windows Server 2008 R2.

4.  **Sélectionner ou créer un partage de fichiers Azure.**

    Sélectionnez un partage de fichiers nouveau ou existant associé au même abonnement que celui de votre locataire Azure AD. Pour plus d’informations sur la création d’un partage de fichiers, consultez [Créer un partage de fichiers dans Azure Files](storage-how-to-create-file-share.md). 
    Pour des performances optimales, Microsoft recommande que votre partage de fichiers soit dans la même région que celle de la machine virtuelle à partir de laquelle vous voulez accéder au partage.

5.  **Vérifier la connectivité d’Azure Files en montant les partages de fichiers Azure à l’aide de votre clé de compte de stockage.**

    Pour vérifier que votre machine virtuelle et le partage de fichiers sont configurés correctement, essayez de monter le partage de fichiers à l’aide de votre clé de compte de stockage. Pour plus d’informations, consultez [Monter un partage de fichiers Azure et accéder au partage dans Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Activer l’authentification Azure AD Domain Services pour votre compte

Pour activer l’authentification Azure AD Domain Services sur SMB pour Azure Files, vous pouvez définir une propriété sur les comptes de stockage créés après le 24 septembre 2018, à l’aide du portail Azure, d’Azure PowerShell ou d’Azure CLI. La définition de cette propriété inscrit le compte de stockage auprès du déploiement Azure AD Domain Services associé. L’authentification Azure AD Domain Services sur SMB est alors activée pour tous les partages de fichiers nouveaux et existants dans le compte de stockage. 

N’oubliez pas que vous pouvez activer l’authentification Azure AD Domain Services sur SMB uniquement après avoir correctement déployé Azure AD Domain Services sur votre locataire Azure AD. Pour plus d’informations, consultez les [prérequis](#prerequisites).

### <a name="azure-portal"></a>Portail Azure

Pour activer l’authentification Azure AD Domain Services sur SMB à l’aide du [portail Azure](https://portal.azure.com), procédez comme suit :

1. Dans le Portail Azure, accédez à votre compte de stockage existant, ou [créez un compte de stockage](../common/storage-quickstart-create-account.md).
2. Dans la section **Paramètres**, sélectionnez **Configuration**.
3. Activez **Azure Active Directory Authentication for Azure Files (preview)** (Azure Active Directory Authentication pour Azure Files (préversion)).

L’image suivante montre comment activer l’authentification Azure AD sur SMB pour votre compte de stockage.

![Activer l’authentification Azure AD sur SMB dans le Portail Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Pour activer l’authentification Azure AD Domain Services sur SMB à l’aide d’Azure PowerShell : Tout d’abord, installez la dernière version du module Az (2.4 ou ultérieur) ou du module Az.Storage (1.5 ou ultérieur). Pour plus d’informations sur l’installation de PowerShell, consultez [Installer Azure PowerShell sur Windows avec PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps) :

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Créez ensuite un compte de stockage, appelez [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) et affectez au paramètre **EnableAzureActiveDirectoryDomainServicesForFile** la valeur **true**. Dans l’exemple ci-dessous, remplacez les valeurs d’espace réservé par vos propres valeurs. (Si vous utilisiez le module en préversion précédent, le paramètre pour l’activation des fonctionnalités est **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Pour activer cette fonctionnalité sur les comptes de stockage existants, utilisez la commande suivante :

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>D’Azure CLI

Pour activer l’authentification Azure AD sur SMB à partir d’Azure CLI 2.0, commencez par installer l’extension `storage-preview` :

```cli-interactive
az extension add --name storage-preview
```
  
Ensuite, créez un compte de stockage, appelez [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) et définissez la propriété `--file-aad` sur **true**. Dans l’exemple ci-dessous, remplacez les valeurs d’espace réservé par vos propres valeurs.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Assigner des autorisations d’accès à une identité 

Pour accéder aux ressources Azure Files à l’aide des informations d’identification Azure AD, une identité (utilisateur, groupe ou principal de service) doit avoir les autorisations nécessaires au niveau du partage. Ce processus est semblable à la spécification des autorisations de partage Windows, où vous spécifiez le type d’accès dont dispose un utilisateur donné sur un partage de fichiers. Les instructions dans cette section montrent comment assigner des autorisations de lecture, d’écriture ou de suppression pour un partage de fichiers à une identité.

Nous avons introduit deux rôles intégrés Azure pour accorder des autorisations au niveau du partage aux utilisateurs : Storage File Data SMB Share Reader (Lecteur de partage SMB de données de fichier de stockage) et Storage File Data SMB Share Contributor (Collaborateur de partage SMB de données de fichier de stockage). 

- **Lecteur de partage SMB de données de fichier de stockage** autorise l’accès en lecture aux partages de fichiers Stockage Azure sur SMB
- **Collaborateur de partage SMB de données de fichier de stockage** autorise l’accès en lecture, écriture et suppression aux partages de fichiers Stockage Azure sur SMB

> [!IMPORTANT]
> Le contrôle administratif total d’un partage de fichiers, y compris la possibilité d’attribuer un rôle à une identité, nécessite l’utilisation de la clé de compte de stockage. Le contrôle administratif n'est pas pris en charge avec les informations d'identification Azure AD. 

Vous pouvez utiliser le portail Azure, PowerShell ou Azure CLI pour attribuer les rôles intégrés à l’identité Azure AD d’un utilisateur afin d’accorder des autorisations au niveau du partage. 

#### <a name="azure-portal"></a>Portail Azure
Pour affecter un rôle RBAC à une identité Azure AD à l’aide du [portail Azure](https://portal.azure.com), procédez comme suit :

1. Dans le portail Azure, accédez à votre partage de fichiers ou [créez un partage de fichiers dans Azure Files](storage-how-to-create-file-share.md).
2. Sélectionnez **Contrôle d’accès (IAM)** .
3. Sélectionnez **Ajouter une attribution de rôle**.
4. Dans le panneau **Ajouter une attribution de rôle**, sélectionnez le rôle intégré approprié (Lecteur de partage SMB de données de fichier de stockage, Collaborateur de partage SMB de données de fichier de stockage) dans la liste déroulante **Rôle**. Conservez pour **Attribuer l’accès à** la valeur par défaut : « Utilisateur, groupe ou principal du service Azure AD », puis sélectionnez l’identité Azure AD cible par son nom ou adresse e-mail. 
5. Enfin, sélectionnez **Enregistrer** pour terminer l’opération d’attribution de rôle.

#### <a name="powershell"></a>PowerShell

La commande PowerShell suivante montre comment attribuer un rôle RBAC à une identité Azure AD, selon le nom de connexion. Pour plus d’informations sur l’attribution de rôles RBAC avec PowerShell, consultez [Gérer l’accès à l’aide de RBAC et Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Quand vous exécutez l’exemple de script suivant, n’oubliez pas de remplacer les valeurs d’espace réservé, y compris les crochets, par vos propres valeurs.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Interface de ligne de commande
  
La commande CLI 2.0 suivante montre comment attribuer un rôle RBAC à une identité Azure AD, selon le nom de connexion. Pour plus d’informations sur l’attribution de rôles RBAC avec Azure CLI, consultez [Gérer l’accès à l’aide de RBAC et Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Quand vous exécutez l’exemple de script suivant, n’oubliez pas de remplacer les valeurs d’espace réservé, y compris les crochets, par vos propres valeurs.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurer les autorisations NTFS sur SMB 
Une fois que vous avez attribué des autorisations au niveau du partage avec RBAC, vous devez attribuer les autorisations NTFS appropriées au niveau du fichier, du répertoire ou de la racine. Les autorisations au niveau du partage représentent le gardien au plus haut niveau qui détermine si un utilisateur peut accéder au partage, tandis que les autorisations NTFS agissent à un niveau plus granulaire pour déterminer les opérations que l’utilisateur peut effectuer au niveau du répertoire ou du fichier. 

Azure Files prend en charge l’ensemble des autorisations NTFS de base et avancées. Vous pouvez voir et configurer les autorisations NTFS sur les répertoires et les fichiers dans un partage de fichiers Azure en montant le partage, puis en exécutant la commande Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl). 

> [!NOTE]
> La préversion prend en charge la consultation des autorisations avec l’Explorateur de fichiers Windows uniquement. La modification des autorisations n’est pas encore prise en charge.

Pour configurer les autorisations NTFS avec des privilèges de superutilisateur, vous devez monter le partage avec votre clé de compte de stockage à partir de votre machine virtuelle jointe à un domaine. Suivez les instructions de la section suivante pour monter un partage de fichiers Azure à partir de l’invite de commandes et configurer les autorisations NTFS de manière appropriée.

Les ensembles d’autorisations suivants sont pris en charge dans le répertoire racine d’un partage de fichiers :

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Monter un partage de fichiers Azure à partir de l’invite de commandes

Utilisez la commande Windows **net use** pour monter le partage de fichiers Azure. N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs. Pour plus d’informations sur le montage des partages de fichiers, consultez [Monter un partage de fichiers Azure et accéder au partage dans Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurer des autorisations NTFS avec icacls
Utilisez la commande Windows suivante pour accorder des autorisations complètes sur tous les répertoires et fichiers du partage de fichiers, y compris le répertoire racine. N’oubliez pas de remplacer les valeurs d’espace réservé affichées entre crochets dans l’exemple par vos propres valeurs.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Pour plus d’informations sur l’utilisation d’icacls afin de définir des autorisations NTFS et sur les différents types d’autorisation pris en charge, consultez les [informations de référence sur la ligne de commande pour icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Monter un partage de fichiers Azure à partir d’une machine virtuelle jointe à un domaine 

Vous allez maintenant vérifier que vous avez effectué correctement les étapes ci-dessus en utilisant vos informations d’identification Azure AD pour accéder à un partage de fichiers Azure à partir d’une machine virtuelle jointe à un domaine. Tout d’abord, connectez-vous à la machine virtuelle à l’aide de l’identité Azure AD à laquelle vous avez accordé les autorisations, comme illustré dans l’image suivante.

![Capture d’écran montrant l’écran de connexion Azure AD pour l’authentification utilisateur](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Ensuite, utilisez la commande suivante pour monter le partage de fichiers Azure. N’oubliez pas de remplacer les valeurs d’espace réservé par vos propres valeurs. Comme vous avez déjà été authentifié, vous n’avez pas besoin de fournir la clé de compte de stockage ni le nom d’utilisateur et le mot de passe Azure AD. Azure AD sur SMB prend en charge l’expérience d’authentification unique à l’aide des informations d’identification Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Vous avez maintenant activé l’authentification Azure AD sur SMB et attribué à un rôle personnalisé qui fournit l’accès à une identité Azure AD au partage de fichiers. Pour accorder l’accès au partage de fichiers à d’autres utilisateurs, suivez les instructions fournies aux étapes 2 et 3.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Files et l’utilisation d’Azure AD sur SMB, consultez ces ressources :

- [Présentation d’Azure Files](storage-files-introduction.md)
- [Vue d’ensemble de l’authentification Azure Active Directory sur SMB pour Azure Files (préversion)](storage-files-active-directory-overview.md)
- [FORUM AUX QUESTIONS](storage-files-faq.md)
