---
title: Utiliser PowerShell pour les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2 (préversion)
description: Utilisez les applets de commande PowerShell pour gérer les répertoires et les listes de contrôle d’accès de fichiers et de répertoires dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: f2a2eaa3224fff117a30dfb742b4f8a35196dba4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973898"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Utiliser PowerShell pour les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2 (préversion)

Cet article explique comment utiliser PowerShell pour créer et gérer des répertoires, des fichiers et des autorisations dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé. 

> [!IMPORTANT]
> Le module PowerShell présenté dans cet article est actuellement en préversion publique.

[Mappage de Gen1 à Gen2](#gen1-gen2-map) | [Envoyer des commentaires](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](data-lake-storage-quickstart-create-account.md) instructions.
> * .NET Framework version 4.7.2 ou ultérieure installé. Consultez [Télécharger .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell version `5.1` ou ultérieure.

## <a name="install-powershell-modules"></a>Installer des modules PowerShell

1. Vérifiez que la version de PowerShell installée est `5.1` ou une version ultérieure à l’aide de la commande suivante. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Pour mettre à niveau votre version de PowerShell, consultez [Mise à niveau des instances Windows PowerShell existantes](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).
    
2. Installez le dernier module **PowerShellGet**. Ensuite, fermez et rouvrez la console PowerShell.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Installez le module **Az.Storage** en préversion.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Pour plus d’informations sur l’installation des modules PowerShell, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="connect-to-the-account"></a>Se connecter au compte

1. Ouvrez une fenêtre Commande Windows PowerShell.

2. Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

   ```powershell
   Connect-AzAccount
   ```

3. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage dans lequel vous souhaitez créer et gérer des répertoires.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

4. Obtenez le compte de stockage.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

   * Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

5. Obtenez le contexte du compte de stockage.

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>Créer un système de fichiers

Un système de fichiers agit comme un conteneur pour vos fichiers. Vous pouvez en créer un à l’aide de l’applet de commande `New-AzDatalakeGen2FileSystem`. 

Cet exemple crée un système de fichiers nommé `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire à l’aide de l’applet de commande `New-AzDataLakeGen2Item`. 

Cet exemple ajoute un répertoire nommé `my-directory` dans un système de fichiers.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Cet exemple ajoute le même répertoire, mais définit également les autorisations, le masque umask, les valeurs de propriété et les valeurs de métadonnées. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Afficher les propriétés du répertoire

Cet exemple obtient un répertoire à l’aide de l’applet de commande `Get-AzDataLakeGen2Item`, puis affiche les valeurs des propriétés dans la console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire à l’aide de l’applet de commande `Move-AzDataLakeGen2Item`.

Cet exemple renomme un répertoire `my-directory` en `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

Cet exemple déplace un répertoire nommé `my-directory` vers un sous-répertoire de `my-directory-2`, nommé `my-subdirectory`. Cet exemple applique également un masque umask au sous-répertoire.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire à l’aide de l’applet de commande `Remove-AzDataLakeGen2Item`.

Cet exemple supprime un répertoire nommé `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Vous pouvez utiliser le paramètre `-Force` pour supprimer le fichier sans invite.

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire

Téléchargez un fichier à partir d’un répertoire à l’aide de l’applet de commande `Get-AzDataLakeGen2ItemContent`.

Cet exemple télécharge un fichier nommé `upload.txt` à partir d’un répertoire nommé `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Listez le contenu d’un répertoire à l’aide de l’applet de commande `Get-AzDataLakeGen2ChildItem`.

Cet exemple liste le contenu d’un répertoire nommé `my-directory`. 

Pour lister le contenu d’un système de fichiers, omettez le paramètre `-Path` dans la commande.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Cet exemple liste le contenu d’un répertoire nommé `my-directory` et inclut les listes ACL dans la liste. Il utilise également le paramètre `-Recurse` pour lister le contenu de tous les sous-répertoires.

Pour lister le contenu d’un système de fichiers, omettez le paramètre `-Path` dans la commande.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Chargez un fichier dans un répertoire à l’aide de l’applet de commande `New-AzDataLakeGen2Item`.

Cet exemple charge un fichier nommé `upload.txt` dans un répertoire nommé `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Cet exemple charge le même fichier, mais définit ensuite les autorisations, le masque umask, les valeurs de propriété et les valeurs de métadonnées du fichier de destination. Cet exemple affiche également ces valeurs dans la console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Afficher les propriétés d’un fichier

Cet exemple obtient un fichier à l’aide de l’applet de commande `Get-AzDataLakeGen2Item`, puis affiche les valeurs des propriétés dans la console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Supprimer un fichier

Supprimez un fichier à l’aide de l’applet de commande `Remove-AzDataLakeGen2Item`.

Cet exemple supprime un fichier nommé `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Vous pouvez utiliser le paramètre `-Force` pour supprimer le fichier sans invite.

## <a name="manage-access-permissions"></a>Gérer les autorisations d’accès

Vous pouvez obtenir, définir et mettre à jour les autorisations d’accès des répertoires et des fichiers.

### <a name="get-directory-and-file-permissions"></a>Obtenir les autorisations d’un répertoire ou d’un fichier

Obtenez la liste ACL d’un répertoire ou d’un fichier à l’aide de l’applet de commande `Get-AzDataLakeGen2Item`.

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

![Obtenir la sortie de liste ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Dans cet exemple, l’utilisateur propriétaire dispose d’autorisations de lecture, d’écriture et d’exécution. Le groupe propriétaire dispose uniquement d’autorisations de lecture et d’exécution. Pour plus d’informations sur les listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Définir les autorisations d’un répertoire ou d’un fichier

Utilisez l’applet de commande `New-AzDataLakeGen2ItemAclObject` pour créer une liste ACL pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs. Ensuite, utilisez l’applet de commande `Update-AzDataLakeGen2Item` pour valider la liste ACL.

Cet exemple définit la liste ACL d’un **répertoire** pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis affiche la liste ACL dans la console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Cet exemple définit la liste ACL d’un **fichier** pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis affiche la liste ACL dans la console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

L’image suivante montre la sortie après la définition de la liste ACL d’un fichier.

![Obtenir la sortie de liste ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Dans cet exemple, l’utilisateur propriétaire et le groupe propriétaire disposent uniquement des autorisations de lecture et d’écriture. Tous les autres utilisateurs disposent des autorisations d’écriture et d’exécution. Pour plus d’informations sur les listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Mettre à jour les autorisations d’un répertoire ou d’un fichier

Utilisez l’applet de commande `Get-AzDataLakeGen2Item` pour obtenir la liste ACL d’un répertoire ou d’un fichier. Ensuite, utilisez l’applet de commande `New-AzDataLakeGen2ItemAclObject` pour créer une entrée de liste de contrôle d’accès. Utilisez l’applet de commande `Update-AzDataLakeGen2Item` pour appliquer la nouvelle liste de contrôle d’accès.

Cet exemple donne à un utilisateur l’autorisation d’écriture et d’exécution sur un répertoire.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
Cet exemple donne à un utilisateur l’autorisation d’écriture et d’exécution sur un fichier.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Définir des autorisations sur tous les éléments d’un système de fichiers

Vous pouvez utiliser `Get-AzDataLakeGen2Item` et le paramètre `-Recurse` ensemble avec l’applet de commande `Update-AzDataLakeGen2Item` pour définir de manière récursive la liste de contrôle d’accès de tous les répertoires et fichiers d’un système de fichiers. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mappage de Gen1 à Gen2

Le tableau suivant montre comment les applets de commande utilisées pour Data Lake Storage Gen1 sont mappées aux applets de commande pour Data Lake Storage Gen2.

|Applet de commande Data Lake Storage Gen1| Applet de commande Data Lake Storage Gen2| Notes |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Par défaut, la cmdlet Get-AzDataLakeGen2ChildItem répertorie uniquement les éléments enfants de premier niveau. Le paramètre -Recurse répertorie les éléments enfants de manière récursive. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Les éléments de sortie de la cmdlet Get-AzDataLakeGen2Item ont les propriétés suivantes : Acl, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|La cmdlet Get-AzDataLakeGen2FileContent télécharge le contenu du fichier dans un fichier local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Cette cmdlet télécharge le contenu du nouveau fichier à partir d’un fichier local.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|La cmdlet Update-AzDataLakeGen2Item met à jour un seul élément, et non de manière récursive. Si vous souhaitez mettre à jour de manière récursive, répertoriez les éléments à l’aide de la cmdlet Get-AzDataLakeStoreChildItem, puis acheminez-les vers la cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|La cmdlet Get-AzDataLakeGen2Item signale une erreur si l’élément n’existe pas.|



## <a name="see-also"></a>Voir aussi

* [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Utilisation d’Azure PowerShell avec Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Applets de commande PowerShell – Stockage](/powershell/module/az.storage)

