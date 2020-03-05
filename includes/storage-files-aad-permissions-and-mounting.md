---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 7246a072c1bf2253b822fca53b0b69700c66221d
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565244"
---
## <a name="assign-access-permissions-to-an-identity"></a>Assigner des autorisations d’accès à une identité

Pour pouvoir accéder à des ressources Azure Files avec l’authentification par identité, il faut que l’identité (utilisateur, groupe ou principal de service) dispose des autorisations nécessaires au niveau du partage. Ce processus est semblable à la spécification des autorisations de partage Windows, où vous spécifiez le type d’accès dont dispose un utilisateur particulier sur un partage de fichiers. Les instructions dans cette section montrent comment assigner des autorisations de lecture, d’écriture ou de suppression pour un partage de fichiers à une identité.

Nous avons introduit trois rôles intégrés Azure pour accorder aux utilisateurs des autorisations au niveau du partage :

- **Lecteur de partage SMB de données de fichier de stockage** autorise l’accès en lecture aux partages de fichiers Stockage Azure sur SMB.
- **Collaborateur de partage SMB de données de fichier de stockage** autorise l’accès en lecture, écriture et suppression aux partages de fichiers Stockage Azure sur SMB.
- **Contributeur élevé de partage SMB de données de fichier de stockage** autorise l’accès NTFS en lecture, écriture, suppression et modification aux partages de fichiers Stockage Azure sur SMB.

> [!IMPORTANT]
> Le contrôle administratif total d’un partage de fichiers, y compris la possibilité d’attribuer un rôle à une identité, nécessite l’utilisation de la clé de compte de stockage. Le contrôle administratif n'est pas pris en charge avec les informations d'identification Azure AD.

Vous pouvez utiliser le portail Azure, PowerShell ou Azure CLI pour attribuer les rôles intégrés à l’identité Azure AD d’un utilisateur afin d’accorder des autorisations au niveau du partage.

> [!NOTE]
> N’oubliez pas de synchroniser vos informations d’identification AD avec Azure AD si vous envisagez d’utiliser AD pour l’authentification. La synchronisation du hachage de mot de passe d’AD vers Azure AD est facultative. L’autorisation au niveau du partage sera accordée à l’identité Azure AD synchronisée à partir d’AD.

#### <a name="azure-portal"></a>Portail Azure
Pour affecter un rôle RBAC à une identité Azure AD à l’aide du [portail Azure](https://portal.azure.com), procédez comme suit :

1. Sur le Portail Azure, accédez à votre partage de fichiers ou [Créez un partage de fichiers](../articles/storage/files/storage-how-to-create-file-share.md).
2. Sélectionnez **Contrôle d’accès (IAM)** .
3. Sélectionnez **Ajouter une attribution de rôle**.
4. Dans le panneau **Ajouter une attribution de rôle**, sélectionnez le rôle intégré approprié (Lecteur de partage SMB de données de fichier de stockage, Collaborateur de partage SMB de données de fichier de stockage) dans la liste **Rôle**. Conservez le paramètre par défaut **Attribuer l’accès à** : **Utilisateur, groupe ou principal du service Azure AD**. Sélectionnez l’identité Azure AD cible par son nom ou son adresse e-mail.
5. Sélectionnez **Enregistrer** pour terminer l’opération d’attribution de rôle.

#### <a name="powershell"></a>PowerShell

L’exemple PowerShell suivant montre comment attribuer un rôle RBAC à une identité Azure AD, selon le nom de connexion. Pour plus d’informations sur l’attribution de rôles RBAC avec PowerShell, consultez [Gérer l’accès à l’aide de RBAC et Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Avant d’exécuter l’exemple de script suivant, n’oubliez pas de remplacer les valeurs d’espace réservé, y compris les crochets, par vos propres valeurs.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Interface de ligne de commande
  
La commande CLI 2.0 suivante montre comment attribuer un rôle RBAC à une identité Azure AD, selon le nom de connexion. Pour plus d’informations sur l’attribution de rôles RBAC avec Azure CLI, consultez [Gérer l’accès à l’aide de RBAC et Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Avant d’exécuter l’exemple de script suivant, n’oubliez pas de remplacer les valeurs d’espace réservé, y compris les crochets, par vos propres valeurs.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurer les autorisations NTFS sur SMB 
Une fois que vous avez attribué des autorisations au niveau du partage avec RBAC, vous devez attribuer les autorisations NTFS appropriées au niveau du fichier, du répertoire ou de la racine. Considérez les autorisations au niveau du partage comme un contrôle d’accès de niveau supérieur qui détermine si un utilisateur peut accéder au partage. En revanche, les autorisations NTFS agissent à un niveau plus granulaire pour déterminer les opérations que l’utilisateur peut effectuer au niveau du répertoire ou du fichier.

Azure Files prend en charge l’ensemble des autorisations NTFS de base et avancées. Vous pouvez voir et configurer les autorisations NTFS sur les répertoires et les fichiers dans un partage de fichiers Azure en montant le partage, puis en utilisant l’Explorateur de fichiers Windows ou en exécutant la commande Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl). 

Pour configurer NTFS avec des autorisations de superutilisateur, vous devez monter le partage avec votre clé de compte de stockage à partir de votre machine virtuelle jointe à un domaine. Suivez les instructions de la section suivante pour monter un partage de fichiers Azure à partir de l’invite de commandes et configurer les autorisations NTFS de manière appropriée.

Les ensembles d’autorisations suivants sont pris en charge dans le répertoire racine d’un partage de fichiers :

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurer des autorisations NTFS avec icacls
Utilisez la commande Windows suivante pour accorder des autorisations complètes sur tous les répertoires et fichiers du partage de fichiers, y compris le répertoire racine. N’oubliez pas de remplacer les valeurs d’espace réservé de l’exemple par vos propres valeurs.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Pour plus d’informations sur l’utilisation d’icacls afin de définir des autorisations NTFS et sur les différents types d’autorisation pris en charge, voir les [informations de référence sur la ligne de commande pour icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Monter un partage de fichiers Azure à partir de l’invite de commandes

Utilisez la commande Windows **net use** pour monter le partage de fichiers Azure. N’oubliez pas de remplacer les valeurs d’espace réservé dans l’exemple suivant par vos propres valeurs. Pour plus d’informations sur le montage de partages de fichiers, consultez [Utilisation d’un partage de fichiers Azure avec Windows](../articles/storage/files/storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurer des autorisations NTFS avec l’Explorateur de fichiers Windows
Utilisez l’Explorateur de fichiers Windows pour accorder des autorisations complètes sur tous les répertoires et fichiers du partage de fichiers, y compris le répertoire racine.

1. Ouvrez l’Explorateur de fichiers Windows, cliquez avec le bouton droit sur le fichier/répertoire, puis sélectionnez **Propriétés**
2. Cliquez sur l’onglet **Sécurité**
3. Cliquez sur le bouton **Modifier...** pour modifier les autorisations
4. Vous pouvez modifier l’autorisation des utilisateurs existants ou cliquer sur **Ajouter...** pour accorder des autorisations à de nouveaux utilisateurs
5. Dans la fenêtre d’invite d’ajout de nouveaux utilisateurs, entrez le nom d’utilisateur cible auquel vous souhaitez accorder l’autorisation dans la zone **Entrez les noms des objets à sélectionner**, puis cliquez sur **Vérifier les noms** pour trouver le nom UPN complet de l’utilisateur cible.
7.  Dans le menu Paramètres, cliquez sur **OK**
8.  Dans l’onglet Sécurité, sélectionnez toutes les autorisations que vous souhaitez accorder à l’utilisateur qui vient d’être ajouté
9.  Cliquez sur **Appliquer**.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Monter un partage de fichiers Azure à partir d’une machine virtuelle jointe à un domaine

Le processus suivant vérifie que vos autorisations de partage de fichiers et d’accès ont été configurées correctement et que vous avez accès à un partage de fichiers Azure à partir d’une machine virtuelle jointe à un domaine :

Connectez-vous à la machine virtuelle à l’aide de l’identité Azure AD à laquelle vous avez accordé les autorisations, comme illustré dans l’image suivante. Si vous avez activé l’authentification AD pour Azure Files, utilisez les informations d’identification AD. Pour l’authentification Azure AD DS, connectez-vous avec les informations d’identification Azure AD.

![Capture d’écran montrant l’écran de connexion Azure AD pour l’authentification utilisateur](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Utilisez la commande suivante pour monter le partage de fichiers Azure. N’oubliez pas de remplacer les valeurs d’espace réservé par vos propres valeurs. Comme vous avez déjà effectué une authentification, vous n’avez pas besoin de fournir la clé de compte de stockage ni les informations d’identification AD ou Azure AD. L’expérience d’authentification unique est prise en charge pour l’authentification avec AD ou bien Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```