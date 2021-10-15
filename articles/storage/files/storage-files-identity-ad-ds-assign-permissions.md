---
title: Contrôler l’accès aux partages de fichiers Azure - Authentification AD DS en local
description: Découvrez comment affecter des autorisations à une identité Active Directory Domain Services qui représente votre compte de stockage. Vous pouvez ainsi contrôler l’accès avec l’authentification basée sur l’identité.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/13/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: cf2d9c2921599680781695631eae9c5276ff53c2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400641"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Deuxième partie : affecter des autorisations au niveau du partage à une identité

Avant de commencer cet article, veillez à lire le précédent intitulé [Activer l’authentification AD DS pour votre compte](storage-files-identity-ad-ds-enable.md).

Une fois que vous avez activé l’authentification AD DS (Active Directory Domain Services) sur votre compte de stockage, vous devez configurer les autorisations au niveau du partage pour pouvoir accéder à vos partages de fichiers. Vous pouvez attribuer des autorisations au niveau du partage de deux manières. Vous pouvez les attribuer à des utilisateurs ou groupes d’utilisateurs Azure AD spécifiques, ou bien les attribuer à toutes les identités authentifiées en tant qu’autorisations au niveau du partage par défaut.

> [!IMPORTANT]
> Le contrôle administratif total d’un partage de fichiers, y compris la possibilité de prendre possession d’un fichier, nécessite l’utilisation de la clé de compte de stockage. Le contrôle administratif n'est pas pris en charge avec les informations d'identification Azure AD.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="which-configuration-should-you-use"></a>Quelle configuration utiliser

La plupart des utilisateurs doivent attribuer des autorisations au niveau du partage à des utilisateurs ou groupes d’utilisateurs Azure AD spécifiques, puis utiliser des listes de contrôle d’accès Windows pour effectuer un contrôle d’accès précis au niveau annuaire et fichier. Il s’agit de la configuration la plus rigoureuse et la plus sécurisée.

Il existe trois scénarios où nous vous recommandons plutôt d’utiliser des autorisations au niveau du partage par défaut attribuées à toutes les identités authentifiées :

- Si vous ne parvenez pas à synchroniser votre AD DS local sur Azure AD, vous pouvez utiliser une autorisation au niveau du partage par défaut. L’attribution d’une autorisation au niveau du partage par défaut vous permet de contourner les exigences de synchronisation, car vous n’avez pas besoin de spécifier l’autorisation pour les identités dans Azure AD. Vous pouvez ensuite utiliser des listes de contrôle d’accès Windows pour appliquer des autorisations précises sur vos fichiers et annuaires.
- L’AD DS local que vous utilisez est synchronisé avec un Azure AD différent de celui dans lequel le partage de fichiers est déployé.
    - C’est généralement le cas lorsque vous gérez des environnements mutualisés. L’utilisation de l’autorisation au niveau du partage par défaut vous permet de contourner l’exigence d’une identité hybride Azure AD. Vous pouvez toujours utiliser des listes de contrôle d’accès Windows sur vos fichiers et annuaires pour l’application d’autorisations précises.
- Vous préférez appliquer l’authentification uniquement à l’aide de listes de contrôle d’accès Windows au niveau fichier et annuaire. 

## <a name="share-level-permissions"></a>Autorisations au niveau du partage

Le tableau suivant présente les autorisations au niveau du partage et la manière dont elles s’alignent avec les rôles RBAC intégrés :

|Rôles intégrés pris en charge  |Description  |
|---------|---------|
|[Lecteur de partage SMB de données de fichier de stockage](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-reader)     |Autorise l’accès en lecture aux fichiers et annuaires dans les partages de fichiers Azure. Ce rôle est similaire à une liste de contrôle d’accès de partage de fichiers pour la lecture sur des serveurs de fichiers Windows. [Plus d’informations](storage-files-identity-auth-active-directory-enable.md)         |
|[Contributeur de partage SMB de données de fichier de stockage](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-contributor)     |Autorise l’accès en lecture, en écriture et en suppression aux fichiers et annuaires dans les partages de fichiers Azure. [Plus d’informations](storage-files-identity-auth-active-directory-enable.md)         |
|[Contributeur élevé de partage SMB de données de fichier de stockage](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-elevated-contributor)     |Autorise la lecture, l’écriture, la suppression et la modification des listes de contrôle d’accès sur des fichiers et annuaires dans des partages de fichiers Azure. Ce rôle est similaire à une liste de contrôle d’accès de partage de fichiers pour la modification sur des serveurs de fichiers Windows. [Plus d’informations](storage-files-identity-auth-active-directory-enable.md)         |


## <a name="share-level-permissions-for-specific-azure-ad-users-or-groups"></a>Autorisations au niveau du partage pour des utilisateurs ou groupes d’utilisateurs Azure AD spécifiques

Si vous envisagez de vous servir d’un utilisateur ou d’un groupe d’utilisateurs Azure AD spécifiques pour accéder aux ressources de partage de fichiers Azure, cette identité doit être une **identité hybride existant tant dans AD DS local que dans Azure AD**. Par exemple, imaginons que vous avez dans votre AD un utilisateur user1@onprem.contoso.com que vous avez synchronisé sur Azure AD en tant que user1@contoso.com à l’aide d’une synchronisation Azure AD Connect. Pour que cet utilisateur puisse accéder à Azure Files, vous devez attribuer les autorisations au niveau du partage à user1@contoso.com. Le même concept s’applique aux groupes ou principaux de service. Pour cette raison, vous devez synchroniser les utilisateurs et les groupes de votre AD sur Azure AD à l’aide d’une synchronisation Azure AD Connect. 

Les autorisations au niveau du partage doivent être affectées à l’identité Azure AD représentant le même utilisateur ou groupe dans votre AD DS pour prendre en charge l’authentification AD DS auprès de votre partage de fichiers Azure. L’authentification et l’autorisation sur des identités qui existent uniquement dans Azure AD, notamment les identités managées Azure (MSI), ne sont pas prises en charge avec l’authentification AD DS.

Vous pouvez utiliser le portail Azure, le module Azure PowerShell ou Azure CLI pour attribuer les rôles intégrés à l’identité Azure AD d’un utilisateur afin d’accorder des autorisations au niveau du partage.

> [!IMPORTANT]
> Les autorisations au niveau du partage prendront jusqu’à trois heures pour prendre effet une fois l’opération terminée. Veuillez patienter pendant la synchronisation des autorisations avant de vous connecter à votre partage de fichiers à l’aide de vos informations d’identification   

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour affecter un rôle Azure à une identité Azure AD par le biais du [portail Azure](https://portal.azure.com), suivez ces étapes :

1. Dans le portail Azure, accédez à votre partage de fichiers ou [créez un partage de fichiers](storage-how-to-create-file-share.md).
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Dans le panneau **Ajouter une attribution de rôle**, dans la liste **Rôle**, sélectionnez le [rôle intégré approprié](#share-level-permissions).
    1. Lecteur de partage SMB des données du fichier de stockage
    1. Collaborateur de partage SMB des données du fichier de stockage
    1. Collaborateur à privilèges élevés de partage SMB des données du fichier de stockage
1.  Conservez le paramètre par défaut **Attribuer l’accès à** : **Utilisateur, groupe ou principal du service Azure AD**. Sélectionnez l’identité Azure AD cible par son nom ou son adresse e-mail. **L’identité Azure AD sélectionnée doit être une identité hybride et ne peut pas être une identité cloud uniquement.** Cela signifie que la même identité est également représentée dans AD DS.
1. Sélectionnez **Enregistrer** pour terminer l’opération d’attribution de rôle.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

L’exemple PowerShell suivant montre comment affecter un rôle Azure à une identité Azure AD, en fonction du nom de connexion. Pour plus d’informations sur l’attribution de rôles Azure avec PowerShell, consultez [Ajouter ou supprimer des attributions de rôles Azure à l’aide du module Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Avant d’exécuter l’exemple de script suivant, remplacez les valeurs d’espace réservé, y compris les crochets, par vos valeurs.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
La commande CLI 2.0 suivante affecte un rôle Azure à une identité Azure AD, selon le nom de connexion. Pour plus d’informations sur l’attribution de rôles Azure avec Azure CLI, consultez [Ajouter ou supprimer des attributions de rôles Azure à l’aide de l’interface de ligne de commande Azure](../../role-based-access-control/role-assignments-cli.md). 

Avant d’exécuter l’exemple de script suivant, n’oubliez pas de remplacer les valeurs d’espace réservé, y compris les crochets, par vos propres valeurs.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="share-level-permissions-for-all-authenticated-identities"></a>Autorisations au niveau du partage pour toutes les identités authentifiées

Au lieu de configurer des autorisations au niveau du partage pour des utilisateurs ou groupes d’utilisateurs Azure AD, vous pouvez ajouter une autorisation au niveau du partage par défaut sur votre compte de stockage. Une autorisation au niveau du partage par défaut attribuée à votre compte de stockage s’applique à tous les partages de fichiers contenus dans le compte de stockage. 

Lorsque vous définissez une autorisation au niveau du partage par défaut, tous les utilisateurs et groupes d’utilisateurs authentifiés disposent de la même autorisation. Les utilisateurs ou groupes d’utilisateurs authentifiés sont identifiés, comme l’identité peut l’être, par rapport à l’AD DS local auquel le compte de stockage est associé. L’autorisation au niveau du partage par défaut étant définie sur **Aucune** lors de l’initialisation, aucun accès aux fichiers et annuaires dans le partage de fichiers Azure n’est autorisé.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Actuellement, vous ne pouvez pas attribuer d’autorisations au compte de stockage via le portail Azure. Utilisez le module Azure PowerShell ou Azure CLI à la place.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Vous pouvez utiliser le script suivant pour configurer des autorisations au niveau du partage par défaut sur votre compte de stockage. Vous ne pouvez activer une autorisation au niveau du partage par défaut que sur des comptes de stockage associés à un service d’annuaire pour l’authentification de fichiers. 

Avant d’exécuter le script suivant, assurez-vous que votre module Az.Storage est de la version 3.7.0 ou d’une version ultérieure.

```azurepowershell
$defaultPermission = "None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice

$account = Set-AzStorageAccount -ResourceGroupName "<resource-group-name-here>" -AccountName "<storage-account-name-here>" -DefaultSharePermission $defaultPermission

$account.AzureFilesIdentityBasedAuth
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez utiliser le script suivant pour configurer des autorisations au niveau du partage par défaut sur votre compte de stockage. Vous ne pouvez activer une autorisation au niveau du partage par défaut que sur des comptes de stockage associés à un service d’annuaire pour l’authentification de fichiers. 

Avant d’exécuter le script suivant, assurez-vous que votre Azure CLI est de la version 2.24.1 ou d’une version ultérieure.

```azurecli
# Declare variables
storageAccountName="YourStorageAccountName"
resourceGroupName="YourResourceGroupName"
defaultPermission="None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice


az storage account update --name $storageAccountName --resource-group $resourceGroupName --default-share-permission $defaultPermission
```
---

## <a name="what-happens-if-you-use-both-configurations"></a>Ce qu’il se passe si vous utilisez les deux configurations

Vous pouvez également attribuer des autorisations à tous les utilisateurs Azure AD authentifiés ainsi qu’à des utilisateurs ou groupes d’utilisateurs Azure AD spécifiques. Dans cette configuration, un utilisateur ou un groupe d’utilisateurs spécifiques disposerait du sur-ensemble d’autorisations résultant de l’autorisation au niveau du partage par défaut et de l’attribution de RBAC. Pour vous aider à comprendre comment cela fonctionne, voici un exemple : supposons que vous avez accordé à un utilisateur le rôle **Lecteur SMB de données de fichier de stockage** sur le partage de fichiers cible. Vous avez également accordé à tous les utilisateurs authentifiés l’autorisation au niveau du partage par défaut **Contributeur élevé de partage SMB de données de fichier de stockage**. Dans cette configuration, cet utilisateur particulier dispose du niveau d’accès **Contributeur élevé de partage SMB de données de fichier de stockage** au partage de fichiers. Les autorisations de niveau supérieur sont toujours prioritaires.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez affecté des autorisations au niveau du partage, vous devez configurer les autorisations au niveau des répertoires et des fichiers. Passez à l’article suivant.

[Troisième partie : configurer les autorisations au niveau des répertoires et des fichiers sur SMB](storage-files-identity-ad-ds-configure-permissions.md)
