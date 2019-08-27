---
title: Transférer des données vers ou depuis le stockage Blob Azure à l’aide d’AzCopy v10 | Microsoft Docs
description: Cet article contient une collection d’exemples de commandes AzCopy qui vous permettent de créer des conteneurs, de copier des fichiers et de synchroniser les répertoires entre les systèmes de fichiers locaux et les conteneurs.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: a95e643d6561f425484a21b5032b1df585d0460b
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900324"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transférer des données avec AzCopy et le stockage Blob

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des données vers, depuis ou entre des comptes de stockage. Cet article contient des exemples de commandes qui fonctionnent avec le stockage Blob.

## <a name="get-started"></a>Prise en main

Consultez l’article [Prise en main d’AzCopy](storage-use-azcopy-v10.md) pour télécharger AzCopy, et découvrez comment vous pouvez fournir des informations d’identification au service de stockage.

> [!NOTE]
> Les exemples de cet article partent du principe que vous vous êtes authentifié à l’aide de la commande `AzCopy login`. AzCopy utilise ensuite votre compte Azure AD pour autoriser l’accès aux données dans le stockage d’objet blob.
>
> Si vous préférez utiliser un jeton SAS pour autoriser l’accès aux données d’objets blob, vous pouvez ajouter ce jeton à l’URL de ressource dans chaque commande AzCopy.
>
> Par exemple : `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Créez un conteneur.

Vous pouvez utiliser la commande `make` AzCopy pour créer un conteneur. Les exemples de cette section créent un conteneur nommé `mycontainer`.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Exemple** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Charger des fichiers

Vous pouvez utiliser la commande `copy` AzCopy pour charger des fichiers et répertoires à partir de votre machine locale.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Charger un fichier
> * Charger un annuaire
> * Charger des fichiers en utilisant des caractères génériques

> [!NOTE]
> AzCopy ne calcule pas automatiquement et ne stocke pas le code de hachage MD5 du fichier. Si vous souhaitez que l’utilitaire AzCopy effectue ces opérations, ajoutez l’indicateur `--put-md5` à chaque commande de copie. De cette façon, lorsque l’objet blob est téléchargé, AzCopy calcule un code de hachage MD5 pour les données téléchargées et vérifie que le code de hachage MD5 stocké dans la propriété `Content-md5` de l’objet blob correspond au code de hachage calculé.

### <a name="upload-a-file"></a>Charger un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Exemple** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy charge par défaut les données dans des objets blob de blocs. Pour charger des fichiers comme des objets blob d’ajout ou de page, utilisez l’indicateur `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Charger un annuaire

Cet exemple copie un répertoire (et tous les fichiers qu’il contient) dans un conteneur d’objets blob. Le conteneur contient alors un répertoire du même nom.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemple** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Pour copier des éléments dans un répertoire du conteneur, spécifiez simplement le nom de ce répertoire dans votre chaîne de commande.

|    |     |
|--------|-----------|
| **Exemple** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Si vous spécifiez le nom d’un répertoire qui n’existe pas dans le conteneur, AzCopy crée un répertoire portant ce nom.

### <a name="upload-the-contents-of-a-directory"></a>Charger le contenu d’un annuaire

Vous pouvez charger le contenu d’un répertoire sans copier le répertoire proprement dit en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Exemple** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Ajoutez l’indicateur `--recursive` pour charger des fichiers dans tous les sous-répertoires.

## <a name="download-files"></a>Télécharger des fichiers

Vous pouvez utiliser la commande `copy` AzCopy pour télécharger des objets blob, des répertoires et des conteneurs sur votre ordinateur local.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Téléchargement d’un fichier
> * Télécharger un annuaire
> * Télécharger des fichiers en utilisant des caractères génériques

> [!NOTE]
> Si la valeur de propriété `Content-md5` d’un objet blob contient un code de hachage, AzCopy calcule un code de hachage MD5 pour les données téléchargées et vérifie que le code de hachage MD5 stocké dans la propriété `Content-md5` de l’objet blob correspond au code de hachage calculé. Si ces valeurs ne correspondent pas, le téléchargement échoue, sauf si vous remplacez ce comportement en ajoutant `--check-md5=NoCheck` ou `--check-md5=LogOnly` à la commande de copie.

### <a name="download-a-file"></a>Téléchargement d’un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Télécharger un annuaire

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Exemple** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Cet exemple illustre la création d’un répertoire nommé `C:\myDirectory\myBlobDirectory` qui contient tous les fichiers téléchargés.

### <a name="download-the-contents-of-a-directory"></a>Télécharger le contenu d’un annuaire

Vous pouvez télécharger le contenu d’un répertoire sans copier le répertoire proprement dit en utilisant le caractère générique (*).

> [!NOTE]
> Actuellement, ce scénario est pris en charge uniquement pour les comptes qui ne disposent pas d’un espace de noms hiérarchique.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Ajoutez l’indicateur `--recursive` pour télécharger des fichiers dans tous les sous-répertoires.

## <a name="copy-blobs-between-storage-accounts"></a>Copier des objets blob entre des comptes de stockage

Vous pouvez utiliser AzCopy pour copier des objets blob vers d’autres comptes de stockage. L’opération de copie étant synchrone, lorsque la commande retourne un résultat, cela indique que tous les fichiers ont été copiés.

AzCopy utilise des [API](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) de [serveur à serveur](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url), de sorte que les données sont copiées directement entre les serveurs de stockage. Ces opérations de copie n’utilisent pas la bande passante réseau de votre ordinateur. Vous pouvez augmenter le débit de ces opérations en définissant la valeur de la variable d’environnement `AZCOPY_CONCURRENCY_VALUE`. Pour en savoir plus, voir [Optimiser le débit](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ce scénario présente les limitations suivantes dans la version actuelle.
>
> - Seuls les comptes qui n’ont pas d’espace de noms hiérarchique sont pris en charge.
> - Vous devez ajouter un jeton SAS à chaque URL source. Si vous fournissez des informations d’identification à l’aide d’Azure Active Directory (AD), vous pouvez omettre le jeton SAS uniquement à partir de l’URL de destination.
>-  Les comptes de stockage d’objet blob de blocs Premium ne prennent pas en charge les niveaux d’accès. Omettez le niveau d’accès d’un objet blob de l’opération de copie en définissant la valeur `s2s-preserve-access-tier` sur `false` (par exemple, `--s2s-preserve-access-tier=false`).

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Copier un objet blob vers un autre compte de stockage
> * Copier un répertoire vers un autre compte de stockage
> * Copier un conteneur vers un autre compte de stockage
> * Copier tous les conteneurs, répertoires et fichiers vers un autre compte de stockage

### <a name="copy-a-blob-to-another-storage-account"></a>Copier un objet blob vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Exemple** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copier un répertoire vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exemple** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Copier un conteneur vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exemple** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copier tous les conteneurs, répertoires et objets blob vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Exemple** | `azcopy copy "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchroniser des fichiers

Vous pouvez synchroniser le contenu d’un système de fichiers local avec un conteneur d’objets blob. La synchronisation est unidirectionnelle. En d’autres termes, vous choisissez lequel de ces deux points de terminaison est la source et lequel est la destination. La synchronisation utilise également un serveur pour les API de serveur.

> [!NOTE]
> Actuellement, ce scénario est pris en charge uniquement pour les comptes qui ne disposent pas d’un espace de noms hiérarchique. La version actuelle de l’utilitaire AzCopy n’effectue pas de synchronisation entre d’autres sources et destinations (exemple : stockage de fichiers ou compartiments Amazon Web Services (AWS) S3).

La commande `sync` compare les noms de fichiers et les timestamps de la dernière modification. Définissez l’indicateur facultatif `--delete-destination` sur la valeur `true` ou `prompt` pour supprimer des fichiers dans le répertoire de destination si ces fichiers n’existent plus dans le répertoire source.

Si vous définissez l’indicateur `--delete-destination` sur `true`, AzCopy supprime les fichiers sans invite. Si vous souhaitez qu’une invite s’affiche avant que l’utilitaire AzCopy ne supprime un fichier, définissez l’indicateur `--delete-destination` sur `prompt`.

> [!NOTE]
> Pour empêcher les suppressions accidentelles, veillez à activer la fonctionnalité de [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) avant d’utiliser l’indicateur `--delete-destination=prompt|true`.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Mettre à jour un conteneur avec les modifications apportées à un système de fichiers local

Dans ce cas, le conteneur est la destination, et le système de fichiers local est la source.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemple** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Mettre à jour un système de fichiers local avec les modifications apportées à un conteneur

Dans ce cas, le système de fichiers local est la destination, et le conteneur est la source.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Exemple** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Étapes suivantes

Plus d’exemples dans ces articles :

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)

- [Tutoriel : Migrer des données locales vers un stockage cloud à l’aide d’AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)

- [Transférer des données avec AzCopy et des compartiments Amazon S3](storage-use-azcopy-s3.md)

- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)
