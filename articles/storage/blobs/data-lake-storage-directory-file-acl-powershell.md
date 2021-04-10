---
title: 'Utiliser PowerShell pour gérer les données : Azure Data Lake Storage Gen2'
description: Utilisez les applets de commande PowerShell pour gérer les répertoires et les fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique activé.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 552d53ff0257105ff61397e281504c5270512319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573861"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Utiliser PowerShell pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser PowerShell pour créer et gérer des répertoires et des fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique.

Pour en savoir plus sur la façon d’obtenir, de définir et de mettre à jour les listes de contrôle d’accès (ACL, Access Control List) des répertoires et des fichiers, consultez [Utiliser PowerShell pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-acl-powershell.md).

[Référence](/powershell/module/Az.Storage/) | [Mappage Gen1 à Gen2](#gen1-gen2-map) | [Envoyer des commentaires](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- .NET Framework version 4.7.2 ou ultérieure installé. Consultez [Télécharger .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- PowerShell version `5.1` ou ultérieure.

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Option 1 : Obtenir l’autorisation à l’aide d’Azure Active Directory (Azure AD)

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

## <a name="create-a-container"></a>Créez un conteneur.

Un conteneur agit comme un système de fichiers pour vos fichiers. Vous pouvez en créer un à l’aide de l’applet de commande `New-AzStorageContainer`. 

Cet exemple permet de créer un conteneur nommé `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire à l’aide de l’applet de commande `New-AzDataLakeGen2Item`. 

Cet exemple ajoute un répertoire nommé `my-directory` à un conteneur.

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
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Pour obtenir le répertoire racine du conteneur, omettez le paramètre `-Path`.

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire à l’aide de l’applet de commande `Move-AzDataLakeGen2Item`.

Cet exemple renomme un répertoire `my-directory` en `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Utilisez le paramètre `-Force` si vous souhaitez remplacer sans invite.

Cet exemple déplace un répertoire nommé `my-directory` vers un sous-répertoire de `my-directory-2`, nommé `my-subdirectory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
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

Listez le contenu d’un répertoire à l’aide de l’applet de commande `Get-AzDataLakeGen2ChildItem`. Vous pouvez utiliser le paramètre facultatif `-OutputUserPrincipalName` pour récupérer le nom (au lieu de l’ID d’objet) des utilisateurs.

Cet exemple liste le contenu d’un répertoire nommé `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

L’exemple suivant répertorie les propriétés `ACL`, `Permissions`, `Group`et `Owner` de chaque élément figurant dans le répertoire. Le paramètre `-FetchProperty` est requis pour obtenir les valeurs de la propriété `ACL`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Pour répertorier les contenus du répertoire racine du conteneur, omettez le paramètre `-Path`.

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
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Pour charger un fichier dans le répertoire racine du conteneur, omettez le paramètre `-Path`.

## <a name="show-file-properties"></a>Afficher les propriétés d’un fichier

Cet exemple obtient un fichier à l’aide de l’applet de commande `Get-AzDataLakeGen2Item`, puis affiche les valeurs des propriétés dans la console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
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

<a id="gen1-gen2-map"></a>

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

- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Applets de commande PowerShell - Stockage](/powershell/module/az.storage)
