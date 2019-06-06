---
title: Transférer des données vers le stockage Azure à partir de compartiments d’Amazon S3 à l’aide d’AzCopy v10 | Microsoft Docs
description: Transfert de données avec AzCopy et Amazon S3 compartiments
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b18c4c039b615c7c88268b6e668df9f7fec9fabf
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687910"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Copier des données à partir de compartiments d’Amazon S3 à l’aide d’AzCopy

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des objets BLOB ou des fichiers vers ou à partir d’un compte de stockage. Cet article vous aide à copier des objets, des répertoires et des compartiments de S3 d’Amazon Web Services (AWS) vers stockage blob Azure à l’aide d’AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choisissez comment vous allez fournir des informations d’identification de l’autorisation

* Pour autoriser avec le stockage Azure, utilisez Azure Active Directory (AD) ou un jeton de Signature d’accès partagé (SAP).

* Pour autoriser avec AWS S3, utilisez une clé d’accès AWS et une clé d’accès secrète.

### <a name="authorize-with-azure-storage"></a>Autoriser avec le stockage Azure

Consultez le [prise en main AzCopy](storage-use-azcopy-v10.md) article pour le téléchargement d’AzCopy, puis choisissez comment vous allez fournir des informations d’identification d’autorisation pour le service de stockage.

> [!NOTE]
> Les exemples de cet article supposent que vous avez été authentifié votre identité à l’aide de la `AzCopy login` commande. AzCopy utilise ensuite votre compte Azure AD pour autoriser l’accès aux données dans le stockage Blob.
>
> Si vous préférez utiliser un jeton SAP pour autoriser l’accès à des données blob, vous pouvez ajouter ce jeton à l’URL de ressource dans chaque commande AzCopy.
>
> Par exemple : `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>L’autorisation auprès d’AWS S3

Recueillir votre clé d’accès AWS et la clé d’accès secrète et puis définissez ces variables d’environnement :

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Copie d’objets, des répertoires et des compartiments

AzCopy utilise le [placer le bloc à partir d’URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, donc les données sont copiées directement entre AWS S3 et les serveurs de stockage. Ces opérations de copie n’utilisent pas la bande passante réseau de votre ordinateur.

### <a name="copy-an-object"></a>Copier un objet

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Exemple** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Exemples dans cet article utilisent le style de chemin d’accès URL pour les compartiments de AWS S3 (par exemple : `http://s3.amazonaws.com/<bucket-name>`). 
>
> Vous pouvez également utiliser également les URL de style hébergé virtuels (par exemple : `http://bucket.s3.amazonaws.com`). 
>
> Pour en savoir plus sur l’hébergement virtuel de compartiments, consultez [virtuel qui héberge des compartiments]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Copier un répertoire

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Exemple** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Copier un compartiment

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Exemple** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copiez tous les compartiments dans toutes les régions

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Exemple** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copiez tous les compartiments dans une région spécifique de S3

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Exemple** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Gérer les différences dans les règles d’affectation de noms d’objet

AWS S3 possède un ensemble différent de conventions d’affectation de noms pour les noms de compartiments par rapport aux conteneurs d’objets blob Azure. Vous pouvez les découvrir [ici](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Si vous choisissez de copier un groupe de compartiments pour un compte de stockage Azure, l’opération de copie peut échouer en raison de différences d’affectation de noms.

AzCopy gère deux des problèmes plus courants qui peuvent se produire ; compartiments qui contiennent des périodes et compartiments contenant des traits d’union consécutifs. Noms de compartiment AWS S3 peuvent contenir des points et des traits d’union consécutifs, mais un conteneur dans Azure ne peut pas. AzCopy remplace les points avec des traits d’union et des traits d’union consécutifs avec un nombre qui représente le nombre de traits d’union consécutifs (par exemple : un compartiment nommé `my----bucket` devient `my-4-bucket`. 

En outre, comme AzCopy copie les fichiers, il vérifie collisions de noms et tente de les résoudre. Par exemple, s’il existe des compartiments avec le nom `bucket-name` et `bucket.name`, AzCopy résout un compartiment nommé `bucket.name` premier à `bucket-name` puis `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Gérer les différences dans les métadonnées d’objet

AWS S3 et Azure permettent différents jeux de caractères dans les noms des clés de l’objet. Vous pouvez en savoir plus sur les caractères que AWS S3 utilise [ici](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Sur le côté Azure, les clés d’objet blob respectent les règles d’affectation de noms pour [ C# identificateurs](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Dans le cadre d’un AzCopy `copy` commande, vous pouvez fournir une valeur pour facultatif le `s2s-invalid-metadata-handle` indicateur qui spécifie la façon dont vous souhaitez gérer les fichiers dont les métadonnées du fichier contient les noms de clé incompatibles. Le tableau suivant décrit chaque valeur de l’indicateur.

| Valeur d’indicateur | Description  |
|--------|-----------|
| **ExcludeIfInvalid** | (Option par défaut) Les métadonnées n’est pas incluses dans l’objet transféré. AzCopy consigne un avertissement. |
| **FailIfInvalid** | Les objets ne sont pas copiés. AzCopy consigne une erreur et inclut cette erreur dans le nombre d’échecs qui apparaît dans le résumé de transfert.  |
| **RenameIfInvalid**  | AzCopy résout la clé de métadonnées non valides et copie l’objet dans Azure à l’aide de la paire de valeur de clé résolu de métadonnées. Pour savoir précisément les étapes prend AzCopy pour renommer les clés d’objet, consultez le [AzCopy comment renomme les clés d’objet](#rename-logic) section ci-dessous. Si AzCopy est impossible de renommer la clé, puis l’objet ne sont pas copié. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Comment AzCopy renomme les clés d’objet

AzCopy effectue ces étapes :

1. Remplace des caractères non valides avec « _ ».

2. Ajoute la chaîne `rename_` au début d’une nouvelle clé valide.

   Cette clé doit être utilisée pour enregistrer les métadonnées d’origine **valeur**.

3. Ajoute la chaîne `rename_key_` au début d’une nouvelle clé valide.
   Cette clé doit être utilisée pour enregistrer les métadonnées d’origine non valide **clé**.
   Vous pouvez utiliser cette clé pour essayer de récupérer les métadonnées dans côté Azure, car la clé de métadonnées est conservé en tant que valeur sur le service de stockage d’objets Blob.

## <a name="next-steps"></a>Étapes suivantes

Trouver plus d’exemples dans ces articles :

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)

- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)

- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)

- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)