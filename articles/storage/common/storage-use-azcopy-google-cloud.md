---
title: Effectuer une copie de Google Cloud Storage vers Stockage Azure à l’aide d’AzCopy | Microsoft Docs
description: Utilisez AzCopy pour copier des données de Google Cloud Storage vers Azure Storage. AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498337"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Copier les données de Google Cloud Storage vers Stockage Azure à l’aide d’AzCopy (préversion)

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article vous aide à copier des objets, des répertoires et des compartiments de Google Cloud Storage vers Stockage Blob Azure avec AzCopy.

> [!IMPORTANT]
> La copie de données de Google Cloud Storage vers le Stockage Azure est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choisissez comment vous allez fournir des informations d’identification d’autorisation.

* Pour autoriser avec Stockage Azure, utilisez Azure Active Directory (AD) ou un jeton SAS (signature d’accès partagé).

* Pour autoriser l’utilisation de Google Cloud Storage, utilisez une clé de compte de service.

### <a name="authorize-with-azure-storage"></a>Autoriser avec Stockage Azure

Consultez l’article [Prise en main d’AzCopy](storage-use-azcopy-v10.md) pour télécharger AzCopy, et découvrez comment vous pouvez fournir des informations d’identification au service de stockage.

> [!NOTE] 
> Les exemples de cet article partent du principe que vous avez fourni des informations d’identification d’autorisation en utilisant Azure AD (Azure Active Directory).
>
> Si vous préférez utiliser un jeton SAS pour autoriser l’accès aux données d’objets blob, vous pouvez ajouter ce jeton à l’URL de ressource dans chaque commande AzCopy. Par exemple : `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autoriser avec Google Cloud Storage

Pour autoriser l’utilisation de Google Cloud Storage, vous utilisez une clé de compte de service. Pour plus d’informations sur la création d’une clé de compte de service, consultez [Création et gestion des clés de compte de service](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Une fois que vous avez obtenu une clé de service, définissez la variable d’environnement `GOOGLE_APPLICATION_CREDENTIALS` sur le chemin d’accès absolu au fichier de clé du compte de service :

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Copier des objets, des répertoires et des compartiments

AzCopy utilise l’API [Placer un bloc à partir d’une URL](/rest/api/storageservices/put-block-from-url) pour copier les données directement d’un serveur Google Cloud Storage à un serveur de stockage. Ces opérations de copie n’utilisent pas la bande passante réseau de votre ordinateur.

> [!TIP]
> Dans les exemples de cette section, les arguments de chemin d’accès sont entre guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

 Ces exemples fonctionnent également avec les comptes qui ont un espace de noms hiérarchique. [L’accès multiprotocole sur Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) vous permet d’utiliser la même syntaxe d’URL (`blob.core.windows.net`) sur ces comptes. 

### <a name="copy-an-object"></a>Copier un objet

Utilisez la même syntaxe d’URL (`blob.core.windows.net`) pour les comptes qui ont un espace de noms hiérarchique.

**Syntaxe**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Exemple**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Copier un répertoire

Utilisez la même syntaxe d’URL (`blob.core.windows.net`) pour les comptes qui ont un espace de noms hiérarchique.

**Syntaxe**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Exemple**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Cet exemple ajoute l’indicateur `--recursive` pour copier des fichiers dans tous les sous-répertoires.

### <a name="copy-the-contents-of-a-directory"></a>Copier le contenu d’un répertoire

Vous pouvez copier le contenu d’un répertoire sans copier le répertoire proprement dit en utilisant le caractère générique (*).

**Syntaxe**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Exemple**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Copier un compartiment de Cloud Storage

Utilisez la même syntaxe d’URL (`blob.core.windows.net`) pour les comptes qui ont un espace de noms hiérarchique.

**Syntaxe**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exemple**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Copier tous les compartiments dans un projet Google Cloud 

Tout d’abord, définissez `GOOGLE_CLOUD_PROJECT` sur l’ID de projet Google Cloud.

Utilisez la même syntaxe d’URL (`blob.core.windows.net`) pour les comptes qui ont un espace de noms hiérarchique.

**Syntaxe**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exemple**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Copier un sous-ensemble de compartiments dans un projet Google Cloud 

Tout d’abord, définissez `GOOGLE_CLOUD_PROJECT` sur l’ID de projet Google Cloud.

Copier un sous-ensemble de compartiments en utilisant un caractère générique (*) dans le nom du compartiment. Utilisez la même syntaxe d’URL (`blob.core.windows.net`) pour les comptes qui ont un espace de noms hiérarchique.

**Syntaxe**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exemple**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Gérer les différences dans les règles d’attributions de noms aux compartiments

Google Cloud Storage possède un ensemble de conventions d’attribution de noms aux compartiments différent des conteneurs d’objets blob Azure. Vous pouvez les découvrir [ici](https://cloud.google.com/storage/docs/naming-buckets). Si vous choisissez de copier un groupe de compartiments dans un compte de stockage Azure, l’opération de copie peut échouer en raison des différences en matière d’attributions de noms.

AzCopy gère trois des problèmes les plus courants qui peuvent survenir : les compartiments contenant des points, les compartiments contenant des tirets consécutifs et les compartiments contenant des traits de soulignement. Les noms de compartiment Google Cloud Storage peuvent contenir des points et des traits d’union consécutifs, ce qui n’est pas le cas d’un conteneur dans Azure. AzCopy remplace les points par des traits d’union et les traits d’union consécutifs par un nombre qui représente le nombre de traits d’union consécutifs (par exemple, un compartiment nommé `my----bucket` devient `my-4-bucket`. Si le nom du compartiment comporte un trait de soulignement (`_`), AzCopy remplace le trait de soulignement par un trait d’union (par exemple : un compartiment nommé `my_bucket` devient `my-bucket`. 

## <a name="handle-differences-in-object-naming-rules"></a>Gérer les différences dans les règles d’attributions de noms aux objets

Google Cloud Storage possède un ensemble de conventions d’attribution de noms aux objets différent des objets blob Azure. Vous pouvez les découvrir [ici](https://cloud.google.com/storage/docs/naming-objects).

Le stockage Azure n’autorise pas les noms d’objets (ou tout segment dans le chemin d’accès du répertoire virtuel) à se terminer par des points (par exemple `my-bucket...`). Les points de fin sont supprimés lorsque l’opération de copie est effectuée. 

## <a name="handle-differences-in-object-metadata"></a>Gérer les différences dans les métadonnées d’objets

Google Cloud Storage et Azure autorisent différents jeux de caractères dans les noms des clés d’objet. Vous pouvez en apprendre plus sur les métadonnées dans Google Cloud Storage [ici](https://cloud.google.com/storage/docs/metadata). Du côté d’Azure, les clés d’objets blob respectent les règles d’attribution de noms pour les [identificateurs C#](/dotnet/csharp/language-reference/).

Dans le cadre d’une commande `copy` AzCopy, vous pouvez fournir une valeur pour l’indicateur `s2s-handle-invalid-metadata` facultatif qui spécifie comment vous voulez gérer les métadonnées du fichier qui contient les noms de clés incompatibles. La table suivante décrit chaque valeur d’indicateur.

| Valeur d’indicateur | Description  |
|--------|-----------|
| **ExcludeIfInvalid** | (Option par défaut) Les métadonnées ne sont pas incluses dans l’objet transféré. AzCopy enregistre un avertissement. |
| **FailIfInvalid** | Les objets ne sont pas copiés. AzCopy enregistre une erreur et l’inclut dans le nombre d’échecs qui apparaît dans le résumé de transfert.  |
| **RenameIfInvalid**  | AzCopy résout la clé de métadonnées non valides et copie l’objet dans Azure à l’aide de la paire valeur/clé des métadonnées résolues. Pour connaître précisément les étapes suivies par AzCopy pour renommer les clés d’objet, consultez la section [Comment AzCopy renomme les clés d’objet](#rename-logic) ci-dessous. Si AzCopy n’est pas en mesure de renommer la clé, l’objet n’est pas copié. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Comment AzCopy renomme les clés d’objet

AzCopy suit cette procédure :

1. Remplace les caractères non valides par « _ ».

2. Ajoute la chaîne `rename_` au début d’une nouvelle clé valide.

   Cette clé doit être utilisée pour enregistrer la **valeur** d’origine des métadonnées.

3. Ajoute la chaîne `rename_key_` au début d’une nouvelle clé valide.
   Cette clé doit être utilisée pour enregistrer la **clé** invalide d’origine des métadonnées.
   Vous pouvez utiliser cette clé pour essayer de récupérer les métadonnées dans Azure, puisque la clé des métadonnées est conservée comme valeur dans le service de stockage d’objets blob.

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez plus d’exemples dans ces articles :

- [Exemples : Charger](storage-use-azcopy-blobs-upload.md)
- [Exemples : Téléchargement](storage-use-azcopy-blobs-download.md)
- [Exemples : Copier entre comptes](storage-use-azcopy-blobs-copy.md)
- [Exemples : Synchroniser](storage-use-azcopy-blobs-synchronize.md)
- [Exemples : Compartiments Amazon S3](storage-use-azcopy-s3.md)
- [Exemples : Azure Files](storage-use-azcopy-files.md)
- [Tutoriel : Migrer des données locales vers un stockage cloud à l’aide d’AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Consultez les articles suivants pour configurer les paramètres, optimiser les performances et résoudre les problèmes :

- [Paramètres de configuration d’AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Optimiser les performances d’AzCopy](storage-use-azcopy-optimize.md)
- [Résoudre les problèmes d’AzCopy V10 dans le stockage Azure à l’aide de fichiers journaux](storage-use-azcopy-configure.md)