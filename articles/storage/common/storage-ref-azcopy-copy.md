---
title: azcopy copy| Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196743"
---
# <a name="azcopy-copy"></a>azcopy copy

Copie les données sources vers un emplacement de destination.

## <a name="synopsis"></a>Synopsis

Les directions prises en charge sont les suivantes :

- Local <-> Objet blob Azure (authentification SAP ou OAuth)
- Local <-> Fichier Azure (authentification SAP pour partage/répertoire)
- Local <-> ADLS Gen 2 (authentification SAP, OAuth ou SharedKey)
- Objet blob Azure (SAP ou public) <-> Objet blob Azure (authentification SAP ou OAuth)
- Fichier Azure (SAP) -> Objet blob de blocs Azure (authentification SAP ou OAuth)
- AWS S3 (clé d’accès) -> Objet blob de blocs Azure (authentification SAP ou OAuth)

Pour plus d’informations, consultez les exemples.

### <a name="advanced"></a>Avancé

AzCopy détecte automatiquement le type de contenu des fichiers lorsque vous les chargez à partir du disque local, en se basant sur l’extension du fichier ou sur son contenu (si aucune extension n’est spécifiée).

La table de recherche intégrée contient peu de données. Toutefois, sur UNIX, elle est augmentée par le ou les fichiers mime.types (si disponibles) du système local, qui peuvent porter les noms suivants :

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Sur Windows, les types MIME sont extraits du Registre. Cette fonctionnalité peut être désactivée à l’aide d’un indicateur. Pour plus d’informations, référez-vous à la section relative aux indicateurs.

> [!IMPORTANT]
> Si vous définissez une variable d’environnement à l’aide de la ligne de commande, la variable sera lisible dans votre historique de ligne de commande. Vous pouvez supprimer de l’historique de la ligne de commande les variables qui contiennent des informations d’identification. Pour empêcher l’affichage des variables dans votre historique, vous pouvez utiliser un script qui invite l’utilisateur à entrer ses informations d’identification et qui définit la variable d’environnement.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemples

Chargez un fichier à l’aide de l’authentification OAuth.

Si vous ne vous êtes pas encore connecté à AzCopy, utilisez la commande `azcopy login` avant d’exécuter la commande suivante.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Comme ci-dessus, mais cette fois-ci, calcule également le hachage MD5 du contenu du fichier et l’enregistre en tant que propriété Content-MD5 de l’objet blob :

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Chargez un fichier à l’aide de l’authentification SAP :

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Chargez un fichier avec une authentification SAP à l’aide du piping (objets blob de blocs uniquement) :

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Chargez l’intégralité d’un répertoire avec l’authentification SAP :

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

or

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Chargez un ensemble de fichiers avec l’authentification SAP à l’aide de caractères génériques :

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Chargez des fichiers et des répertoires avec l’authentification SAP à l’aide de caractères génériques :

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Téléchargez un fichier à l’aide de l’authentification OAuth.

Si vous ne vous êtes pas encore connecté à AzCopy, utilisez la commande `azcopy login` avant d’exécuter la commande suivante.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Téléchargez un fichier à l’aide de l’authentification SAP :

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Téléchargez un fichier avec une authentification SAP à l’aide du piping (objets blob de blocs uniquement) :

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Téléchargez l’intégralité d’un répertoire avec l’authentification SAP :

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Téléchargez un ensemble de fichiers avec l’authentification SAP à l’aide de caractères génériques :

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Téléchargez des fichiers et des répertoires avec l’authentification SAP à l’aide de caractères génériques :

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Copiez un objet blob avec authentification SAP vers un autre objet blob avec SAP :

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiez un objet blob avec authentification SAP vers un autre objet blob avec jeton OAuth.

Si vous ne vous êtes pas encore connecté à AzCopy, utilisez la commande `azcopy login` avant d’exécuter la commande suivante. Le jeton OAuth est utilisé pour accéder au compte de stockage de destination.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copiez l’intégralité d’un répertoire à partir d’un répertoire virtuel d’objets blob avec authentification SAP vers un autre répertoire virtuel d’objets blob avec authentification SAP :

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copiez l’intégralité des données d’un compte d’objets blob avec authentification SAP vers un autre compte d’objets blob avec authentification SAP :

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Copiez un objet à partir de S3 avec une clé d’accès vers un objet blob avec authentification SAP :

Définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiez l’intégralité d’un répertoire entier à partir de S3 avec une clé d’accès vers le répertoire virtuel d’objets blob avec authentification SAP :

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Pour plus d’informations sur ce que signifie [folder] pour S3, consultez https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html. Définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source S3.

Copiez tous les compartiments du service S3 avec une clé d’accès vers le compte d’objets blob avec authentification SAP :

Définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Copiez tous les compartiments d’une région S3 avec une clé d’accès vers le compte d’objets blob avec authentification SAP :

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source S3.

## <a name="options"></a>Options

|Option|Description|
|--|--|
|--blob-type (chaîne)|Définit le type d’objet blob au niveau de la destination. Utilisé pour charger des objets blob et lors de la copie de données d’un compte à l’autre (par défaut « None »).|
|--block-blob-tier (chaîne)|Permet de charger un objet blob de blocs dans le stockage Azure à l’aide de ce niveau d’objet blob (par défaut : « None »).|
|--block-size-mb float |Taille de bloc (spécifiée en Mio) utilisée lors du chargement dans le stockage Azure et le téléchargement à partir du stockage Azure. La valeur par défaut est calculée automatiquement en fonction de la taille du fichier. Les fractions décimales sont autorisées (par exemple : 0,25).|
|--cache-control (chaîne)|Définit l’en-tête cache-control. Retourné au moment du téléchargement.|
|--check-md5 (chaîne)|Spécifie la manière dont les hachages MD5 doivent être validés lors du téléchargement. Disponible uniquement lors du téléchargement. Options disponibles : NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (par défaut « FailIfDifferent »)|
|--content-disposition (chaîne)|Définit l’en-tête content-disposition. Retourné au moment du téléchargement.|
|--content-encoding (chaîne)|Définit l’en-tête content-encoding. Retourné au moment du téléchargement.|
|--content-language (chaîne)|Définit l’en-tête content-language. Retourné au moment du téléchargement.|
|--content-type (chaîne) |Spécifie le type de contenu du fichier. Implique l’utilisation de no-guess-mime-type. Retourné au moment du téléchargement.|
|--exclude (chaîne)|Permet d’exclure des fichiers lors de la copie. Prend en charge l’utilisation de *.|
|--exclude-blob-type (chaîne)|(Facultatif) Spécifie le type d’objet blob (BlockBlob/PageBlob/AppendBlob) à exclure lors de la copie d’objets blob à partir du conteneur ou du compte. L’utilisation de cet indicateur ne s’applique pas à la copie de données à partir d’un service non Azure. Si vous avez plusieurs objets blob, vous devez les séparer par un point-virgule (« ; »).|
|--follow-symlinks|Permet de suivre les liens symboliques lorsque vous effectuez un chargement à partir d’un système de fichiers local.|
|--from-to (chaîne)|(Facultatif) Spécifie la combinaison destination-source. Par exemple : LocalBlob, BlobLocal, LocalBlobFS.|
|-h, --help|Affiche l’aide de la commande copy. |
|--log-level (chaîne)|Définit le niveau de détail pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes/réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie) (par défaut : « INFO »).|
|--metadata (chaîne)|Permet de charger dans le stockage Azure des paires clé-valeur en tant que métadonnées.|
|--no-guess-mime-type|Empêche AzCopy de détecter le type de contenu (content-type) en fonction de l’extension ou du contenu du fichier.|
|--overwrite|Si cet indicateur a la valeur true, remplace les fichiers ou objets blob qui sont en conflit dans la destination (par défaut : true).|
|--page-blob-tier (chaîne) |Permet de charger un objet blob de pages dans le stockage Azure à l’aide de ce niveau d’objet blob (par défaut : « None »).|
|--preserve-last-modified-time|Disponible uniquement quand la destination est un système de fichiers.|
|--put-md5|Crée un hachage MD5 de chaque fichier, puis enregistre le hachage en tant que propriété Content-MD5 de l’objet blob ou du fichier de destination (par défaut, le hachage n’est pas créé.) Disponible uniquement lors du chargement.|
|--recursive|Examine le contenu des sous-répertoires de manière récursive lors d’un chargement à partir du système de fichiers local.|
|--s2s-detect-source-changed|Vérifie si la source a changé après l’énumération. Pour les copies S2S, puisque la source est une ressource distante, le processus permettant de vérifier si la source a changé implique des coûts de requête supplémentaires.|
|--s2s-handle-invalid-metadata (chaîne) |Spécifie la manière dont les clés de métadonnées non valides sont gérées. Options disponibles : ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid (par défaut : « ExcludeIfInvalid »).|
|--s2s-preserve-access-tier|Préserve le niveau d’accès lors d’une copie de service à service. Pour vérifier si le compte de stockage de destination prend en charge la définition du niveau d’accès, consultez [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](../blobs/storage-blob-storage-tiers.md). Si la définition du niveau d’accès n’est pas prise en charge, utilisez s2sPreserveAccessTier=false pour contourner la copie du niveau d’accès.  (par défaut : true).|
|--s2s-preserve-properties|Préserve l’intégralité des propriétés lors d’une copie de service à service. Pour une source de fichier non unique de type S3 ou Fichier Azure, puisque l’opération de liste ne retourne pas l’intégralité des propriétés des objets et des fichiers, pour conserver toutes les propriétés, AzCopy doit envoyer une requête supplémentaire pour chaque objet et fichier. (par défaut : true).|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
