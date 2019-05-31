---
title: Transférer des données vers ou depuis le stockage Blob Azure à l’aide d’AzCopy v10 | Microsoft Docs
description: Cet article constituée une collection d’AzCopy exemple commandes qui vous aident à créent des conteneurs, copiez les fichiers et synchroniser des dossiers entre les systèmes de fichiers locaux et des conteneurs.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299411"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transfert de données avec AzCopy et le stockage Blob

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des données vers, depuis ou entre comptes de stockage. Cet article contient des exemples de commandes qui fonctionnent avec le stockage d’objets Blob.

## <a name="get-started"></a>Prise en main

Consultez le [prise en main AzCopy](storage-use-azcopy-v10.md) article pour en savoir plus sur les méthodes que vous pouvez fournir des informations d’identification d’autorisation pour le service de stockage et la téléchargez AzCopy.

> [!NOTE]
> Les exemples de cet article supposent que vous avez été authentifié votre identité à l’aide de la `AzCopy login` commande. AzCopy utilise ensuite votre compte Azure AD pour autoriser l’accès aux données dans le stockage Blob.
>
> Si vous préférez utiliser un jeton SAP pour autoriser l’accès à des données blob, vous pouvez ajouter ce jeton à l’URL de ressource dans chaque commande AzCopy.
>
> Par exemple : `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Créez un conteneur.

Vous pouvez utiliser AzCopy `make` commande pour créer un conteneur. Les exemples de cette section créent un conteneur nommé `mycontainer`.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Exemple** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Charger des fichiers

Vous pouvez utiliser AzCopy `copy` commande pour charger des fichiers et dossiers à partir de votre ordinateur local.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Charger un fichier
> * Charger un dossier
> * Télécharger des fichiers à l’aide de caractères génériques

> [!NOTE]
> AzCopy n’automatiquement calculer et stocker le code de hachage md5 du fichier. Si vous souhaitez AzCopy pour ce faire, puis ajoutez le `--put-md5` indicateur à chaque commande de copie. De cette façon, lorsque l’objet blob est téléchargé, AzCopy calcule un hachage MD5 pour télécharger les données et vérifie que le hachage MD5 stocké dans l’objet blob `Content-md5` propriété correspond au hachage calculé.

### <a name="upload-a-file"></a>Charger un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Exemple** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy par défaut charge les données dans les objets BLOB de blocs. Pour télécharger des fichiers comme objets BLOB d’ajout ou d’objets BLOB de pages utiliser l’indicateur `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-folder"></a>Charger un dossier

Cet exemple copie un dossier (et tous les fichiers dans ce dossier) dans un conteneur d’objets blob. Le résultat est un dossier dans le conteneur par le même nom.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemple** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Pour copier vers un dossier au sein du conteneur, spécifiez simplement le nom de ce dossier dans votre chaîne de commande.

|    |     |
|--------|-----------|
| **Exemple** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

Si vous spécifiez le nom d’un dossier qui n’existe pas dans le conteneur, AzCopy crée un nouveau dossier portant ce nom.

### <a name="upload-the-contents-of-a-folder"></a>Téléchargez le contenu d’un dossier

Vous pouvez télécharger le contenu d’un dossier sans copier le dossier qui contient lui-même en utilisant le caractère générique (*).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **Exemple** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> Ajouter le `--recursive` indicateur pour charger des fichiers dans tous les sous-dossiers.

## <a name="download-files"></a>Télécharger des fichiers

Vous pouvez utiliser AzCopy `copy` commande pour télécharger des objets BLOB, des dossiers et des conteneurs sur votre ordinateur local.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Téléchargement d’un fichier
> * Un dossier de téléchargement
> * Télécharger des fichiers à l’aide de caractères génériques

> [!NOTE]
> Si le `Content-md5` valeur de propriété d’un objet blob contient un hachage, AzCopy calcule un hachage MD5 pour les données téléchargées et vérifie que le hachage MD5 stocké dans l’objet blob `Content-md5` propriété correspond au hachage calculé. Si ces valeurs ne correspondent pas, le téléchargement échoue, sauf si vous remplacez ce comportement en ajoutant `--check-md5=NoCheck` ou `--check-md5=LogOnly` à la commande de copie.

### <a name="download-a-file"></a>Téléchargement d’un fichier

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Un dossier de téléchargement

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **Exemple** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

Cet exemple de résultats dans un dossier nommé `C:\myFolder\myBlobFolder` qui contient tous les fichiers téléchargés.

### <a name="download-the-contents-of-a-folder"></a>Télécharger le contenu d’un dossier

Vous pouvez télécharger le contenu d’un dossier sans copier le dossier qui contient lui-même en utilisant le caractère générique (*).

> [!NOTE]
> Actuellement, ce scénario est pris en charge uniquement pour les comptes qui n’ont pas un espace de noms hiérarchique.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **Exemple** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> Ajouter le `--recursive` indicateur permettant de télécharger les fichiers dans tous les sous-dossiers.

## <a name="copy-blobs-between-storage-accounts"></a>Copier des objets BLOB entre des comptes de stockage

Vous pouvez utiliser AzCopy pour copier des objets BLOB vers d’autres comptes de stockage. L’opération de copie étant synchrone lorsque la commande est retournée, qui indique que tous les fichiers ont été copiés.

> [!NOTE]
> Actuellement, ce scénario est pris en charge uniquement pour les comptes qui n’ont pas un espace de noms hiérarchique.

AzCopy utilise le [placer le bloc à partir d’URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, donc les données sont copiées directement entre les serveurs de stockage. Ces opérations de copie n’utilisent pas la bande passante réseau de votre ordinateur.

Cette section contient les exemples suivants :

> [!div class="checklist"]
> * Copier un objet blob vers un autre compte de stockage
> * Copier un dossier vers un autre compte de stockage
> * Copier un conteneurs vers un autre compte de stockage
> * Copiez tous les conteneurs, les dossiers et les fichiers vers un autre compte de stockage

### <a name="copy-a-blob-to-another-storage-account"></a>Copier un objet blob vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Exemple** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>Copier un dossier vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **Exemple** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Copier un conteneurs vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exemple** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>Copiez tous les conteneurs, les dossiers et les fichiers vers un autre compte de stockage

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Exemple** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchroniser des fichiers

Vous pouvez synchroniser le contenu d’un système de fichiers local à un conteneur d’objets blob. Vous pouvez également synchroniser un conteneur d’objets blob dans un système de fichiers local sur votre ordinateur. La synchronisation est unidirectionnelle. En d’autres termes, vous choisissez parmi ces deux points de terminaison est la source et celui constitue la destination.

> [!NOTE]
> La version actuelle de AzCopy ne synchronise entre d’autres sources et les destinations (par exemple : Stockage de fichiers ou des compartiments de Amazon Web Services (AWS) S3).

Le `sync` commande compare les noms de fichiers et les horodateurs de la dernière modification. Définir le `--delete-destination` indicateur facultatif pour une valeur de `true` ou `prompt` pour supprimer des fichiers dans le dossier de destination si ces fichiers n’existent plus dans le dossier source.

Si vous définissez la `--delete-destination` indicateur `true` AzCopy supprime les fichiers sans fournir une invite de commandes. Si vous souhaitez une invite pour apparaître avant AzCopy supprime un fichier, définissez la `--delete-destination` indicateur `prompt`.

> [!NOTE]
> Pour empêcher les suppressions accidentelles, veillez à activer la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) fonctionnalité avant d’utiliser le `--delete-destination=prompt|true` indicateur.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Synchroniser un conteneur pour un système de fichiers local

Dans ce cas, le système de fichiers local devient la source et le conteneur est la destination.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemple** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Synchroniser un système de fichiers local vers un conteneur

Dans ce cas, le conteneur devient la source et le système de fichiers local est la destination.

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **Exemple** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **Exemple** (espace de noms hiérarchique) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>Étapes suivantes

Trouver plus d’exemples dans ces articles :

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)

- [Tutoriel : Migrer des données localement pour le stockage cloud à l’aide d’AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transfert de données avec AzCopy et stockage fichier](storage-use-azcopy-files.md)

- [Transfert de données avec AzCopy et Amazon S3 compartiments](storage-use-azcopy-s3.md)

- [Configurer, optimiser et résoudre les problèmes d’AzCopy](storage-use-azcopy-configure.md)