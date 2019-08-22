---
title: Transférer des données vers ou depuis Azure Files à l’aide d’AzCopy v10 | Microsoft Docs
description: Transférez des données avec AzCopy et le stockage de fichiers.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648754"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transférer des données avec AzCopy et le stockage de fichiers 

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article contient des exemples de commandes qui fonctionnent avec Azure Files.

Avant de commencer, consultez l’article [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md) pour télécharger AzCopy et vous familiariser avec l’outil.

## <a name="create-file-shares"></a>Créer des partages de fichiers

Vous pouvez utiliser la commande `make` AzCopy pour créer un partage de fichiers. L’exemple dans cette section crée un partage de fichiers nommé `myfileshare`.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Exemple** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Charger des fichiers

Vous pouvez utiliser la commande `copy` AzCopy pour charger des fichiers et répertoires à partir de votre machine locale.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Charger un fichier
> * Charger un annuaire
> * Charger des fichiers en utilisant des caractères génériques

> [!NOTE]
> AzCopy ne calcule pas automatiquement et ne stocke pas le code de hachage MD5 du fichier. Si vous souhaitez que l’utilitaire AzCopy effectue ces opérations, ajoutez l’indicateur `--put-md5` à chaque commande de copie. De cette façon, lorsque le fichier est téléchargé, AzCopy calcule un code de hachage MD5 pour les données téléchargées et vérifie que le code de hachage MD5 stocké dans la propriété `Content-md5` du fichier correspond au code de hachage calculé.

### <a name="upload-a-file"></a>Charger un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Exemple** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Charger un annuaire

Cet exemple copie un répertoire (et tous les fichiers qu’il contient) dans un partage de fichiers. Le partage de fichiers contient alors un répertoire du même nom.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Exemple** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Pour copier des éléments dans un répertoire du partage de fichiers, spécifiez simplement le nom de ce répertoire dans votre chaîne de commande.

|    |     |
|--------|-----------|
| **Exemple** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Si vous spécifiez le nom d’un répertoire qui n’existe pas dans le partage de fichiers, AzCopy crée un répertoire portant ce nom.

### <a name="upload-the-contents-of-a-directory"></a>Charger le contenu d’un annuaire

Vous pouvez charger le contenu d’un répertoire sans copier le répertoire proprement dit en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Exemple** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Ajoutez l’indicateur `--recursive` pour charger des fichiers dans tous les sous-répertoires.

## <a name="download-files"></a>Télécharger des fichiers

Vous pouvez utiliser la commande `copy` AzCopy pour télécharger des fichiers, des répertoires et des partages de fichiers sur votre machine locale.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Téléchargement d’un fichier
> * Télécharger un annuaire
> * Télécharger des fichiers en utilisant des caractères génériques

> [!NOTE]
> Si la valeur de propriété `Content-md5` d’un fichier contient un code de hachage, AzCopy calcule un code de hachage MD5 pour les données téléchargées et vérifie que le code de hachage MD5 stocké dans la propriété `Content-md5` du fichier correspond au code de hachage calculé. Si ces valeurs ne correspondent pas, le téléchargement échoue, sauf si vous remplacez ce comportement en ajoutant `--check-md5=NoCheck` ou `--check-md5=LogOnly` à la commande de copie.

### <a name="download-a-file"></a>Téléchargement d’un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Télécharger un annuaire

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Exemple** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Cet exemple illustre la création d’un répertoire nommé `C:\myDirectory\myFileShareDirectory` qui contient tous les fichiers téléchargés.

### <a name="download-the-contents-of-a-directory"></a>Télécharger le contenu d’un annuaire

Vous pouvez télécharger le contenu d’un répertoire sans copier le répertoire proprement dit en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Ajoutez l’indicateur `--recursive` pour télécharger des fichiers dans tous les sous-répertoires.

## <a name="next-steps"></a>Étapes suivantes

Plus d’exemples dans ces articles :

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)

- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)

- [Transférer des données avec AzCopy et des compartiments Amazon S3](storage-use-azcopy-s3.md)

- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)