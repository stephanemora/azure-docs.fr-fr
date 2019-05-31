---
title: Transférer des données vers ou à partir de fichiers Azure à l’aide d’AzCopy v10 | Microsoft Docs
description: Transfert de données avec AzCopy et stockage fichier.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247108"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transfert de données avec AzCopy et stockage fichier 

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des objets BLOB ou des fichiers vers ou à partir d’un compte de stockage. Cet article contient des exemples de commandes qui fonctionnent avec Azure Files.

Avant de commencer, consultez le [prise en main AzCopy](storage-use-azcopy-v10.md) article pour le téléchargement d’AzCopy et vous familiariser avec l’outil.

## <a name="create-file-shares"></a>Créer des partages de fichiers

Vous pouvez utiliser AzCopy `make` commande pour créer un partage de fichiers. L’exemple dans cette section crée un partage de fichiers nommé `myfileshare`.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Exemple** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Charger des fichiers

Vous pouvez utiliser AzCopy `copy` commande pour charger des fichiers et dossiers à partir de votre ordinateur local.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Charger un fichier
> * Charger un dossier
> * Télécharger des fichiers à l’aide de caractères génériques

> [!NOTE]
> AzCopy n’automatiquement calculer et stocker le code de hachage md5 du fichier. Si vous souhaitez AzCopy pour ce faire, puis ajoutez le `--put-md5` indicateur à chaque commande de copie. De cette façon, lorsque le fichier est téléchargé, AzCopy calcule un hachage MD5 pour télécharger les données et vérifie que le hachage MD5 stocké dans le fichier `Content-md5` propriété correspond au hachage calculé.

### <a name="upload-a-file"></a>Charger un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Exemple** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>Charger un dossier

Cet exemple copie un dossier (et tous les fichiers dans ce dossier) dans un partage de fichiers. Le résultat est un dossier dans le partage de fichiers par le même nom.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Exemple** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Pour copier vers un dossier dans le partage de fichiers, spécifiez simplement le nom de ce dossier dans votre chaîne de commande.

|    |     |
|--------|-----------|
| **Exemple** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Si vous spécifiez le nom d’un dossier qui n’existe pas dans le partage de fichiers, AzCopy crée un nouveau dossier portant ce nom.

### <a name="upload-the-contents-of-a-folder"></a>Téléchargez le contenu d’un dossier

Vous pouvez télécharger le contenu d’un dossier sans copier le dossier qui contient lui-même en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **Exemple** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Ajouter le `--recursive` indicateur pour charger des fichiers dans tous les sous-dossiers.

## <a name="download-files"></a>Télécharger des fichiers

Vous pouvez utiliser AzCopy `copy` partages de commande pour télécharger des fichiers, dossiers et fichiers sur votre ordinateur local.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Téléchargement d’un fichier
> * Un dossier de téléchargement
> * Télécharger des fichiers à l’aide de caractères génériques

> [!NOTE]
> Si le `Content-md5` valeur de propriété d’un fichier contient un hachage, AzCopy calcule un hachage MD5 pour les données téléchargées et vérifie que le hachage MD5 stocké dans le fichier `Content-md5` propriété correspond au hachage calculé. Si ces valeurs ne correspondent pas, le téléchargement échoue, sauf si vous remplacez ce comportement en ajoutant `--check-md5=NoCheck` ou `--check-md5=LogOnly` à la commande de copie.

### <a name="download-a-file"></a>Téléchargement d’un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Un dossier de téléchargement

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **Exemple** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

Cet exemple de résultats dans un dossier nommé `C:\myFolder\myFileShareFolder` qui contient tous les fichiers téléchargés.

### <a name="download-the-contents-of-a-folder"></a>Télécharger le contenu d’un dossier

Vous pouvez télécharger le contenu d’un dossier sans copier le dossier qui contient lui-même en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> Ajouter le `--recursive` indicateur permettant de télécharger les fichiers dans tous les sous-dossiers.

## <a name="next-steps"></a>Étapes suivantes

Trouver plus d’exemples dans ces articles :

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)

- [Transfert de données avec AzCopy et le stockage blob](storage-use-azcopy-blobs.md)

- [Transfert de données avec AzCopy et Amazon S3 compartiments](storage-use-azcopy-s3.md)

- [Configurer, optimiser et résoudre les problèmes d’AzCopy](storage-use-azcopy-configure.md)