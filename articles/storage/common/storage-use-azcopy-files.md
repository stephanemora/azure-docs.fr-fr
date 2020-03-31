---
title: Transférer des données vers ou depuis Azure Files à l’aide d’AzCopy v10 | Microsoft Docs
description: Transférez des données avec AzCopy et le stockage de fichiers.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526686"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transférer des données avec AzCopy et le stockage de fichiers 

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article contient des exemples de commandes qui fonctionnent avec Azure Files.

Avant de commencer, consultez l’article [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md) pour télécharger AzCopy et vous familiariser avec l’outil.

## <a name="create-file-shares"></a>Créer des partages de fichiers

Vous pouvez utiliser la commande [azcopy make](storage-ref-azcopy-make.md) pour créer un partage de fichiers. L’exemple dans cette section crée un partage de fichiers nommé `myfileshare`.

> [!TIP]
> Dans les exemples de cette section, les arguments de chemin d’accès sont entre guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Exemple** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Pour obtenir des informations de référence détaillées, consultez [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Charger des fichiers

Vous pouvez utiliser la commande [azcopy copy](storage-ref-azcopy-copy.md) pour charger des fichiers et répertoires à partir de votre machine locale.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Charger un fichier
> * Charger un annuaire
> * Charger le contenu d’un annuaire
> * Charger un fichier spécifique

> [!NOTE]
> AzCopy ne calcule pas automatiquement et ne stocke pas le code de hachage MD5 du fichier. Si vous souhaitez que l’utilitaire AzCopy effectue ces opérations, ajoutez l’indicateur `--put-md5` à chaque commande de copie. De cette façon, lorsque le fichier est téléchargé, AzCopy calcule un code de hachage MD5 pour les données téléchargées et vérifie que le code de hachage MD5 stocké dans la propriété `Content-md5` du fichier correspond au code de hachage calculé.

Pour obtenir des informations de référence détaillées, consultez [azcopy copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Dans les exemples de cette section, les arguments de chemin d’accès sont entre guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

### <a name="upload-a-file"></a>Charger un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exemple** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Vous pouvez également charger un fichier en plaçant un caractère générique (*) n’importe où dans le chemin ou le nom de fichier. Par exemple : `'C:\myDirectory\*.txt'` ou `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Charger un annuaire

Cet exemple copie un répertoire (et tous les fichiers qu’il contient) dans un partage de fichiers. Le partage de fichiers contient alors un répertoire du même nom.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemple** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Pour copier des éléments dans un répertoire du partage de fichiers, spécifiez simplement le nom de ce répertoire dans votre chaîne de commande.

|    |     |
|--------|-----------|
| **Exemple** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Si vous spécifiez le nom d’un répertoire qui n’existe pas dans le partage de fichiers, AzCopy crée un répertoire portant ce nom.

### <a name="upload-the-contents-of-a-directory"></a>Charger le contenu d’un annuaire

Vous pouvez charger le contenu d’un répertoire sans copier le répertoire proprement dit en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exemple** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Ajoutez l’indicateur `--recursive` pour charger des fichiers dans tous les sous-répertoires.

### <a name="upload-specific-files"></a>Charger des fichiers spécifiques

Vous pouvez spécifier des noms de fichiers complets ou utiliser des noms partiels avec des caractères génériques (*).

#### <a name="specify-multiple-complete-file-names"></a>Spécifier plusieurs noms de fichiers complets

Utilisez la commande [azcopy copy](storage-ref-azcopy-copy.md) avec l’option `--include-path`. Séparez les noms de fichiers à l’aide d’un point-virgule (`;`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exemple** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Dans cet exemple, AzCopy transfère le répertoire `C:\myDirectory\photos` et le fichier `C:\myDirectory\documents\myFile.txt`. Vous devez inclure l’option `--recursive` pour transférer tous les fichiers dans le répertoire `C:\myDirectory\photos`.

Vous pouvez également exclure des fichiers à l’aide de l’option `--exclude-path`. Pour plus d’informations, consultez la documentation de référence sur [azcopy copy](storage-ref-azcopy-copy.md).

#### <a name="use-wildcard-characters"></a>Utiliser des caractères génériques

Utilisez la commande [azcopy copy](storage-ref-azcopy-copy.md) avec l’option `--include-pattern`. Spécifiez des noms partiels qui incluent les caractères génériques. Séparez les noms à l’aide d’un point-virgule (`;`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemple** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Vous pouvez également exclure des fichiers à l’aide de l’option `--exclude-pattern`. Pour plus d’informations, consultez la documentation de référence sur [azcopy copy](storage-ref-azcopy-copy.md).

Les options `--include-pattern` et `--exclude-pattern` s’appliquent uniquement aux noms de fichiers, et non au chemin.  Si vous souhaitez copier tous les fichiers texte qui existent dans une arborescence de répertoires, utilisez l’option `–recursive` pour obtenir la totalité de l’arborescence de répertoires, puis utilisez `–include-pattern` et spécifiez `*.txt` pour obtenir tous les fichiers texte.

## <a name="download-files"></a>Télécharger les fichiers

Vous pouvez utiliser la commande [azcopy copy](storage-ref-azcopy-copy.md) pour télécharger des fichiers, des répertoires et des partages de fichiers sur votre machine locale.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Téléchargement d’un fichier
> * Télécharger un annuaire
> * Télécharger le contenu d’un annuaire
> * Télécharger des fichiers spécifiques

> [!NOTE]
> Si la valeur de propriété `Content-md5` d’un fichier contient un code de hachage, AzCopy calcule un code de hachage MD5 pour les données téléchargées et vérifie que le code de hachage MD5 stocké dans la propriété `Content-md5` du fichier correspond au code de hachage calculé. Si ces valeurs ne correspondent pas, le téléchargement échoue, sauf si vous remplacez ce comportement en ajoutant `--check-md5=NoCheck` ou `--check-md5=LogOnly` à la commande de copie.

Pour obtenir des informations de référence détaillées, consultez [azcopy copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Dans les exemples de cette section, les arguments de chemin d’accès sont entre guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

### <a name="download-a-file"></a>Téléchargement d’un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exemple** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Télécharger un annuaire

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exemple** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Cet exemple illustre la création d’un répertoire nommé `C:\myDirectory\myFileShareDirectory` qui contient tous les fichiers téléchargés.

### <a name="download-the-contents-of-a-directory"></a>Télécharger le contenu d’un annuaire

Vous pouvez télécharger le contenu d’un répertoire sans copier le répertoire proprement dit en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exemple** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Ajoutez l’indicateur `--recursive` pour télécharger des fichiers dans tous les sous-répertoires.

### <a name="download-specific-files"></a>Télécharger des fichiers spécifiques

Vous pouvez spécifier des noms de fichiers complets ou utiliser des noms partiels avec des caractères génériques (*).

#### <a name="specify-multiple-complete-file-names"></a>Spécifier plusieurs noms de fichiers complets

Utilisez la commande [azcopy copy](storage-ref-azcopy-copy.md) avec l’option `--include-path`. Séparez les noms de fichiers à l’aide d’un point-virgule (`;`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemple** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Dans cet exemple, AzCopy transfère le répertoire `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` et le fichier `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Vous devez inclure l’option `--recursive` pour transférer tous les fichiers dans le répertoire `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

Vous pouvez également exclure des fichiers à l’aide de l’option `--exclude-path`. Pour plus d’informations, consultez la documentation de référence sur [azcopy copy](storage-ref-azcopy-copy.md).

#### <a name="use-wildcard-characters"></a>Utiliser des caractères génériques

Utilisez la commande [azcopy copy](storage-ref-azcopy-copy.md) avec l’option `--include-pattern`. Spécifiez des noms partiels qui incluent les caractères génériques. Séparez les noms à l’aide d’un point-virgule (`;`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemple** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Vous pouvez également exclure des fichiers à l’aide de l’option `--exclude-pattern`. Pour plus d’informations, consultez la documentation de référence sur [azcopy copy](storage-ref-azcopy-copy.md).

Les options `--include-pattern` et `--exclude-pattern` s’appliquent uniquement aux noms de fichiers, et non au chemin.  Si vous souhaitez copier tous les fichiers texte qui existent dans une arborescence de répertoires, utilisez l’option `–recursive` pour obtenir la totalité de l’arborescence de répertoires, puis utilisez `–include-pattern` et spécifiez `*.txt` pour obtenir tous les fichiers texte.

## <a name="copy-files-between-storage-accounts"></a>Copier des fichiers entre des comptes de stockage

Vous pouvez utiliser AzCopy pour copier des fichiers vers d’autres comptes de stockage. L’opération de copie étant synchrone, lorsque la commande retourne un résultat, cela indique que tous les fichiers ont été copiés.

AzCopy utilise des [API](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [serveur à serveur](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url), de sorte que les données sont copiées directement entre les serveurs de stockage. Ces opérations de copie n’utilisent pas la bande passante réseau de votre ordinateur. Vous pouvez augmenter le débit de ces opérations en définissant la valeur de la variable d’environnement `AZCOPY_CONCURRENCY_VALUE`. Pour en savoir plus, voir [Optimiser le débit](storage-use-azcopy-configure.md#optimize-throughput).

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Copier un fichier vers un autre compte de stockage
> * Copier un répertoire vers un autre compte de stockage
> * Copier un partage de fichiers vers un autre compte de stockage
> * Copier tous les partages de fichiers, répertoires et fichiers vers un autre compte de stockage

Pour obtenir des informations de référence détaillées, consultez [azcopy copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Dans les exemples de cette section, les arguments de chemin d’accès sont entre guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

### <a name="copy-a-file-to-another-storage-account"></a>Copier un fichier vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exemple** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copier un répertoire vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemple** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copier un partage de fichiers vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemple** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copier tous les partages de fichiers, répertoires et fichiers vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exemple** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Synchroniser des fichiers

Vous pouvez synchroniser le contenu d’un partage de fichiers avec un partage de fichiers. Vous pouvez également synchroniser le contenu d’un répertoire dans un partage de fichiers avec celui d’un répertoire situé dans un autre partage de fichiers. La synchronisation est unidirectionnelle. En d’autres termes, vous choisissez lequel de ces deux points de terminaison est la source et lequel est la destination. La synchronisation utilise également un serveur pour les API de serveur.

> [!NOTE]
> Actuellement, ce scénario est pris en charge uniquement pour les comptes qui ne disposent pas d’un espace de noms hiérarchique. La version actuelle d’AzCopy n’est pas synchronisée entre Azure Files et Stockage Blob.

La commande [sync](storage-ref-azcopy-sync.md) compare les noms de fichiers et les horodatages de la dernière modification. Définissez l’indicateur facultatif `--delete-destination` sur la valeur `true` ou `prompt` pour supprimer des fichiers dans le répertoire de destination si ces fichiers n’existent plus dans le répertoire source.

Si vous définissez l’indicateur `--delete-destination` sur `true`, AzCopy supprime les fichiers sans invite. Si vous souhaitez qu’une invite s’affiche avant que l’utilitaire AzCopy ne supprime un fichier, définissez l’indicateur `--delete-destination` sur `prompt`.

Pour obtenir des informations de référence détaillées, consultez [azcopy sync](storage-ref-azcopy-sync.md).

> [!TIP]
> Dans les exemples de cette section, les arguments de chemin d’accès sont entre guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Mettre à jour un partage de fichiers avec les modifications apportées à un autre partage de fichiers

Le premier partage de fichiers qui s’affiche dans cette commande est la source. Le deuxième est la destination.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemple** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Mettre à jour un répertoire avec les modifications apportées à un répertoire dans un autre partage de fichiers

Le premier répertoire qui s’affiche dans cette commande est la source. Le deuxième est la destination.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exemple** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Étapes suivantes

Plus d’exemples dans ces articles :

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)

- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)

- [Transférer des données avec AzCopy et des compartiments Amazon S3](storage-use-azcopy-s3.md)

- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)
