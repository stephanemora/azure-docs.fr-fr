---
title: azcopy sync | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086026"
---
# <a name="azcopy-sync"></a>azcopy sync

Réplique l’emplacement source vers l’emplacement de destination.

## <a name="synopsis"></a>Synopsis

Les heures de dernière modification sont utilisées à des fins de comparaison. Le fichier est ignoré si l’heure de dernière modification de l’emplacement de destination est plus récente.

Les paires prises en charge sont les suivantes :

- Local <-> Objet blob Azure (authentification SAP ou OAuth)
- Objet blob Azure <-> objet blob Azure (la source doit inclure une signature d’accès partagé (SAP) ou est accessible au public ; l’authentification SAP ou OAuth peut être utilisée pour la destination)
- Fichier Azure <-> fichier Azure (la source doit inclure une signature d’accès partagé (SAP) ou est accessible au public ; l’authentification SAP doit être utilisée pour la destination)

Il existe plusieurs différences entre la commande sync et la commande copy :

1. Par défaut, l’indicateur récursif possède la valeur true et la synchronisation copie tous les sous-répertoires. La synchronisation copie uniquement les fichiers de niveau supérieur à l’intérieur d’un répertoire si l’indicateur récursif possède la valeur false.
2. Lors de la synchronisation entre répertoires virtuels, ajoutez une barre oblique finale au chemin d’accès (voir exemples) s’il existe un objet blob portant le même nom que l’un des répertoires virtuels.
3. Si l’indicateur « deleteDestination » est défini sur true ou prompt, la synchronisation supprime les fichiers et objets blob de destination qui ne sont pas présents au niveau de la source.

## <a name="related-conceptual-articles"></a>Articles conceptuels associés

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)
- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)
- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avancé

Si vous n’indiquez pas d’extension de fichier, AzCopy détecte automatiquement le type de contenu des fichiers lorsque vous les chargez à partir du disque local, en se basant sur l’extension du fichier ou sur son contenu (si aucune extension n’est spécifiée).

La table de recherche intégrée contient peu de données. Toutefois, sur UNIX, elle est augmentée par le ou les fichiers mime.types (si disponibles) du système local, qui peuvent porter les noms suivants :

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Sur Windows, les types MIME sont extraits du Registre.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exemples

Synchroniser un fichier :

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> Le blob de destination *doit* exister. Utilisez `azcopy copy` pour copier un fichier unique qui n’existe pas encore dans la destination. Sinon, l’erreur suivante s’affiche : `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`.

Comme ci-dessus, mais cette fois-ci, calcule également le hachage MD5 du contenu du fichier et l’enregistre en tant que propriété Content-MD5 de l’objet blob :

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchroniser l’intégralité d’un répertoire, y compris ses sous-répertoires (notez que la récursivité est activée par défaut) :

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

or

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchroniser uniquement les principaux fichiers d’un répertoire, mais pas ses sous-répertoires :

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchroniser un sous-ensemble de fichiers dans un répertoire (par exemple, uniquement les fichiers .jpg et PDF, ou uniquement les fichiers dont le nom est « exactName ») :

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Synchroniser l’intégralité d’un répertoire, mais exclure certains fichiers (par exemple, tous ceux dont le nom commence par « foo » ou se termine par « bar ») :

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Synchronisez un objet blob :

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synchroniser un répertoire virtuel :

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synchronisez un répertoire virtuel portant le même nom qu’un objet blob (ajoutez une barre oblique finale au chemin d’accès pour lever l’ambiguïté) :

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synchronisez un répertoire Azure File (même syntaxe que l’objet blob) :

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Si les indicateurs include/exclude sont utilisés ensemble, seuls les fichiers correspondant aux caractères génériques include sont examinés. Ceux qui correspondent aux caractères génériques exclude seront toujours ignorés.

## <a name="options"></a>Options

**--block-size-mb** float         Taille de bloc (spécifiée en Mio) utilisée lors du chargement dans Stockage Azure ou le téléchargement à partir de Stockage Azure. La valeur par défaut est calculée automatiquement en fonction de la taille du fichier. Les fractions décimales sont autorisées (par exemple : 0,25).

**--check-md5** string         Spécifie la manière dont les hachages MD5 doivent être validés lors du téléchargement. Disponible uniquement lors du téléchargement. Valeurs disponibles : NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (par défaut « FailIfDifferent ») (par défaut « FailIfDifferent »)

**--delete-destination** string   Détermine si les fichiers de l’emplacement de destination qui ne sont pas présents dans l’emplacement source doivent être supprimés. Valeurs possibles : true, false ou prompt. Si la valeur est définie sur prompt, l’utilisateur est invité à répondre à une question avant de planifier la suppression des fichiers et des objets blob. (par défaut : « false ») (par défaut : « false »)

**--exclude-attributes** string   (Windows uniquement) Exclut les fichiers dont les attributs correspondent à la liste d’attributs. Par exemple : A;S;R

**--exclude-path** string Exclut ces chemins lors de la copie. Cette option ne prend pas en charge les caractères génériques (*). Vérifie le préfixe de chemin relatif (Par exemple : myFolder;myFolder/subDirName/file.pdf.) En cas d’utilisation combinée avec la traversée de comptes, les chemins n’incluent pas le nom du conteneur.

**--exclude-pattern** string      Exclut les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : \*.jpg;\*.pdf;exactName

**-h, --help**                        Aide pour la synchronisation

**--include-attributes** string   (Windows uniquement) Inclut uniquement les fichiers dont les attributs correspondent à la liste d’attributs. Par exemple : A;S;R

**--include-pattern** string      Inclut uniquement les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : \*.jpg;\*.pdf;exactName

**--log-level** string            Définit le niveau de détail pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes et réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie). (par défaut : INFO) (par défaut : « INFO »)

**--put-md5**                     Crée un hachage MD5 de chaque fichier, puis enregistre le hachage en tant que propriété Content-MD5 de l’objet blob ou du fichier de destination. (par défaut, le hachage n’est pas créé.) Disponible uniquement lors du chargement.

**--recursive**                   True par défaut. Examine les sous-répertoires de manière récursive lors de la synchronisation des répertoires. (par défaut : true) (par défaut : true)

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
