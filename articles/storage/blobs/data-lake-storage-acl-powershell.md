---
title: Utiliser PowerShell pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2
description: Utilisez les applets de commande PowerShell pour gérer les listes de contrôle d’accès dans des comptes de stockage dotés d’un espace de noms hiérarchique activé.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104702539"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utiliser PowerShell pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser PowerShell pour récupérer, définir et mettre à jour les listes de contrôle d’accès de répertoires et de fichiers. 

L’héritage des listes ACL est déjà disponible pour les nouveaux éléments enfants créés sous un répertoire parent. Toutefois, vous pouvez également ajouter, mettre à jour et supprimer des listes de contrôle d’accès de manière récursive au niveau des éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant. 

[Référence](/powershell/module/Az.Storage/) | [Exemples de listes de contrôle d’accès récursives](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) | [Envoyer des commentaires](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Azure CLI version `2.6.0` ou ultérieure.

- Une des autorisations de sécurité suivantes :

  - Un [principal de sécurité](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD) provisionné qui a reçu le rôle [Propriétaire des données Blob de stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur cible, du groupe de ressources parent ou de l’abonnement.  

  - Utilisateur propriétaire du conteneur ou du répertoire cible auquel vous envisagez d’appliquer les paramètres ACL. Pour définir des listes de contrôle d’accès de façon récursive, cela inclut tous les éléments enfants du conteneur ou du répertoire cible.
  
  - Clé du compte de stockage.

## <a name="install-the-powershell-module"></a>Installer le module PowerShell

1. Vérifiez que la version de PowerShell installée est `5.1` ou une version ultérieure à l’aide de la commande suivante.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Pour mettre à niveau votre version de PowerShell, consultez [Mise à niveau des instances Windows PowerShell existantes](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

2. Installez le module **Az.Storage**.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Pour plus d’informations sur l’installation des modules PowerShell, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="connect-to-the-account"></a>Se connecter au compte

Choisissez la façon dont vous souhaitez que vos commandes obtiennent l’autorisation pour le compte de stockage. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Option 1 : Obtenir l’autorisation à l’aide d’Azure Active Directory (AD)

> [!NOTE]
> Si vous utilisez Azure Active Directory (Azure AD) pour autoriser l’accès, assurez-vous que le [rôle Propriétaire des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) est attribué à votre principal de sécurité. Pour en savoir plus sur l’application des autorisations de liste de contrôle d’accès et les conséquences de leur modification, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Avec cette approche, le système garantit que votre compte d’utilisateur dispose des autorisations de contrôle d’accès en fonction du rôle Azure (Azure RBAC) appropriées et des autorisations de liste de contrôle d’accès (ACL).

1. Ouvrez une fenêtre de commande Windows PowerShell, puis connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

   ```powershell
   Connect-AzAccount
   ```

2. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage dans lequel vous souhaitez créer et gérer des répertoires. Dans cet exemple, remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. Obtenez le contexte du compte de stockage.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Option n°2 : Obtenir l’autorisation à l’aide de la clé de compte de stockage

Avec cette approche, le système ne vérifie pas les autorisations Azure RBAC ou ACL. Récupérez le contexte du compte de stockage à l’aide d’une clé de compte.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>Obtenir les listes de contrôle d’accès

Obtenez la liste ACL d’un répertoire ou d’un fichier à l’aide de l’applet de commande `Get-AzDataLakeGen2Item`.

Cet exemple obtient la liste ACL du répertoire racine d’un **conteneur**, puis l’affiche sur la console.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Cet exemple obtient la liste ACL d’un **répertoire**, puis affiche cette liste ACL dans la console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Cet exemple obtient la liste ACL d’un **fichier**, puis affiche cette liste ACL dans la console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

L’image suivante montre la sortie après l’obtention de la liste ACL d’un répertoire.

![Obtenir une sortie de liste ACL pour un répertoire](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Dans cet exemple, l’utilisateur propriétaire dispose d’autorisations de lecture, d’écriture et d’exécution. Le groupe propriétaire dispose uniquement d’autorisations de lecture et d’exécution. Pour plus d’informations sur les listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Définir les listes de contrôle d’accès

Lorsque vous *définissez* une liste de contrôle d’accès (ACL), vous **remplacez** l’ensemble de l’ACL, y compris toutes ses entrées. Si vous souhaitez modifier le niveau d’autorisation d’un principal de sécurité ou ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter d’autres entrées existantes, vous devez *mettre à jour* l’ACL à la place. Pour mettre à jour une liste de contrôle d’accès au lieu de la remplacer, consultez la section [Mettre à jour une liste de contrôle d’accès](#update-acls) de cet article.  

Si vous choisissez de *définir* la liste de contrôle d’accès, vous devez ajouter une entrée pour l’utilisateur propriétaire, une entrée pour le groupe propriétaire et une entrée pour tous les autres utilisateurs. Pour en savoir plus sur l’utilisateur propriétaire, le groupe propriétaire et tous les autres utilisateurs, consultez [Utilisateurs et identités](data-lake-storage-access-control.md#users-and-identities).

Cette section vous montre comment :

- Définir une liste de contrôle d’accès
- Définir des listes de contrôle d’accès de façon récursive

### <a name="set-an-acl"></a>Définir une liste de contrôle d’accès

Utilisez l’applet de commande `set-AzDataLakeGen2ItemAclObject` pour créer une liste ACL pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs. Ensuite, utilisez l’applet de commande `Update-AzDataLakeGen2Item` pour valider la liste ACL.

Cet exemple définit la liste ACL du répertoire racine d’un **conteneur** pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis affiche cette liste sur la console.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Cet exemple définit la liste ACL d’un **répertoire** pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis affiche la liste ACL dans la console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Si vous souhaitez définir une entrée de liste de contrôle d’accès **par défaut**, utilisez le paramètre **-DefaultScope** quand vous exécutez la commande **Set-AzDataLakeGen2ItemAclObject**. Par exemple : `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`. 

Cet exemple définit la liste ACL d’un **fichier** pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis affiche la liste ACL dans la console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Pour définir l’ACL d’un groupe ou d’un utilisateur spécifique, utilisez leurs ID d’objet respectifs. Par exemple, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

L’image suivante montre la sortie après la définition de la liste ACL d’un fichier.

![Obtenir une sortie de liste ACL pour un fichier](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Dans cet exemple, l’utilisateur propriétaire et le groupe propriétaire disposent uniquement des autorisations de lecture et d’écriture. Tous les autres utilisateurs disposent des autorisations d’écriture et d’exécution. Pour plus d’informations sur les listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Définir des listes de contrôle d’accès de façon récursive

Définissez des listes de contrôle d’accès de manière récursive à l’aide de la cmdlet **Set-AzDataLakeGen2AclRecursive**.

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. Ces entrées donnent à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donnent au groupe propriétaire uniquement des autorisations de lecture et d’exécution, et ne donnent à tous les autres aucun accès. La dernière entrée de la liste de contrôle d’accès dans cet exemple donne à un utilisateur spécifique avec l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » des autorisations de lecture et d’exécution.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Si vous souhaitez définir une entrée de liste de contrôle d’accès **par défaut**, utilisez le paramètre **-DefaultScope** quand vous exécutez la commande **Set-AzDataLakeGen2ItemAclObject**. Par exemple : `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Pour voir un exemple qui définit des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’article de référence [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

## <a name="update-acls"></a>Mettre à jour les ACL

Lorsque vous *mettez à jour* une liste de contrôle d’accès, vous modifiez l’ACL au lieu de la remplacer. Par exemple, vous pouvez ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter les autres principaux de sécurité listés dans l’ACL.  Pour remplacer la liste de contrôle d’accès au lieu de la mettre à jour, consultez la section [Mettre à jour une liste de contrôle d’accès](#set-acls) de cet article.

Pour mettre à jour une liste de contrôle d’accès, créez un nouvel objet ACL avec l’entrée de l’ACL à mettre à jour, puis utilisez cet objet dans l’opération de mise à jour de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à mettre à jour.

Cette section vous montre comment :

- Mettre à jour une ACL
- Mettre à jour les listes de contrôle d’accès de manière récursive

### <a name="update-an-acl"></a>Mettre à jour une ACL

Tout d’abord, obtenez la liste de contrôle d’accès. Ensuite, utilisez l’applet de commande `set-AzDataLakeGen2ItemAclObject` pour ajouter ou mettre à jour une entrée de liste de contrôle d’accès. Utilisez l’applet de commande `Update-AzDataLakeGen2Item` pour valider la liste de contrôle d’accès.

Cet exemple crée ou met à jour la liste de contrôle d’accès sur un **répertoire** pour un utilisateur.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Si vous souhaitez mettre à jour une entrée de liste de contrôle d’accès **par défaut**, utilisez le paramètre **-DefaultScope** lors de l’exécution de la commande **Set-AzDataLakeGen2ItemAclObject**. Par exemple : `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Mettre à jour les listes de contrôle d’accès de manière récursive

Mettez à jour des listes de contrôle d’accès de manière récursive à l’aide de la cmdlet **Update-AzDataLakeGen2AclRecursive**.

Cet exemple met à jour une entrée de liste de contrôle d’accès avec l’autorisation d’écriture.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Pour définir l’ACL d’un groupe ou d’un utilisateur spécifique, utilisez leurs ID d’objet respectifs. Par exemple, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Pour voir un exemple qui met à jour des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’article de référence [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive).

## <a name="remove-acl-entries"></a>Supprimer des entrées de liste de contrôle d’accès

Cette section vous montre comment :

- Supprimer une entrée de liste de contrôle d’accès
- Supprimer des entrées de liste de contrôle d’accès (ACL) de manière récursive

### <a name="remove-an-acl-entry"></a>Supprimer une entrée de liste de contrôle d’accès

Cet exemple supprime une entrée d’une liste de contrôle d’accès existante.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>Supprimer des entrées de liste de contrôle d’accès (ACL) de manière récursive

Vous pouvez supprimer une ou plusieurs entrées de liste de contrôle d’accès (ACL) de manière récursive. Pour supprimer une entrée de liste de contrôle d’accès, créez un nouvel objet ACL pour l’entrée ACL à supprimer, puis utilisez cet objet dans l’opération de suppression de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à supprimer.

Supprimez des entrées de liste de contrôle d’accès à l’aide de la cmdlet **Remove-AzDataLakeGen2AclRecursive**.

Cet exemple supprime une entrée de liste de contrôle d’accès dans le répertoire racine du conteneur.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Si vous souhaitez supprimer une entrée de liste de contrôle d’accès **par défaut**, utilisez le paramètre **-DefaultScope** lors de l’exécution de la commande **Set-AzDataLakeGen2ItemAclObject**. Par exemple : `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Pour voir un exemple qui supprime des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’article de référence [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive).

## <a name="recover-from-failures"></a>Récupérer suite à des échecs

Vous pouvez rencontrer des erreurs d’exécution ou d’autorisation lors de la modification récursive des listes de contrôle d’accès. 

Pour les erreurs d’exécution, redémarrez le processus à partir du début. Des erreurs d’autorisation peuvent se produire si le principal de sécurité ne dispose pas des autorisations suffisantes pour modifier la liste de contrôle d’accès d’un répertoire ou d’un fichier qui se trouve dans la hiérarchie de répertoires en cours de modification. Résolvez le problème d’autorisation, puis choisissez de reprendre le processus à partir du point de défaillance à l’aide d’un jeton de continuation ou de redémarrer le processus à partir du début. Vous n’êtes pas obligé d’utiliser le jeton de continuation si vous préférez redémarrer à partir du début. Vous pouvez réappliquer les entrées de liste de contrôle d’accès sans incidence négative.

Cet exemple retourne des résultats à la variable, puis canalise les entrées ayant échoué vers une table mise en forme.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

En fonction de la sortie du tableau, vous pouvez corriger les erreurs d’autorisation, puis reprendre l’exécution à l’aide du jeton de continuation.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Pour voir un exemple qui définit des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’article de référence [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

Si vous souhaitez que le processus se termine sans être interrompu par des erreurs d’autorisation, vous pouvez le spécifier.

Cet exemple utilise le paramètre `ContinueOnFailure` pour que l’exécution continue même si l’opération rencontre une erreur d’autorisation. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Pour voir un exemple qui définit des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’article de référence [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Voir aussi

- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Applets de commande PowerShell - Stockage](/powershell/module/az.storage)
- [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listes de contrôle d’accès (ACL) dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
