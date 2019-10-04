---
title: azcopy sync | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196751"
---
# <a name="azcopy-sync"></a>azcopy sync

Réplique l’emplacement source vers l’emplacement de destination.

## <a name="synopsis"></a>Synopsis

Les heures de dernière modification sont utilisées à des fins de comparaison. Le fichier est ignoré si l’heure de dernière modification de l’emplacement de destination est plus récente.

Les paires prises en charge sont les suivantes :

- Local <-> Objet blob Azure (authentification SAP ou OAuth)

Il existe plusieurs différences entre la commande sync et la commande copy :

  1. L’indicateur recursive est activé par défaut.
  2. La source et la destination ne doivent pas contenir de caractères génériques (comme * ou ?).
  3. Les indicateurs include et exclude peuvent constituer des listes de caractères génériques correspondant aux noms des fichiers. Pour voir un exemple, reportez-vous à la section Exemple.
  4. Si des fichiers ou des objets blob de l’emplacement de destination ne sont pas présents dans l’emplacement source, l’utilisateur est invité à les supprimer.

     Cette invite peut être rendue silencieuse à l’aide des indicateurs correspondants permettant de répondre automatiquement à la question de suppression.

### <a name="advanced"></a>Avancé

AzCopy détecte automatiquement le type de contenu des fichiers lorsque vous les chargez à partir du disque local, en se basant sur l’extension du fichier ou sur son contenu (si aucune extension n’est spécifiée).

La table de recherche intégrée contient peu de données. Toutefois, sur UNIX, elle est augmentée par le ou les fichiers mime.types (si disponibles) du système local, qui peuvent porter les noms suivants :

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Sur Windows, les types MIME sont extraits du Registre.

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>Exemples

Synchroniser un fichier :

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

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

> [!NOTE]
> Si les indicateurs include/exclude sont utilisés ensemble, seuls les fichiers correspondant aux caractères génériques include sont examinés. Ceux qui correspondent aux caractères génériques exclude seront toujours ignorés.

## <a name="options"></a>Options

|Option|Description|
|--|--|
|--block-size-mb float|Taille de bloc (spécifiée en Mio) utilisée lors du chargement dans le stockage Azure ou du téléchargement à partir du stockage Azure. La valeur par défaut est calculée automatiquement en fonction de la taille du fichier. Les fractions décimales sont autorisées (par exemple : 0,25).|
|--check-md5 (chaîne)|Spécifie la manière dont les hachages MD5 doivent être validés lors du téléchargement. Disponible uniquement lors du téléchargement. Valeurs disponibles : NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (par défaut « FailIfDifferent »)|
|--delete-destination (chaîne)|Définit si les fichiers de l’emplacement de destination qui ne sont pas présents dans l’emplacement source doivent être supprimés. Valeurs possibles : true, false ou prompt. Si la valeur est définie sur prompt, l’utilisateur est invité à répondre à une question avant de planifier la suppression des fichiers et des objets blob. (par défaut : « false »)|
|--exclude (chaîne)|Exclut les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName.|
|-h, --help|Affiche l’aide de la commande sync.|
|--include (chaîne)|Inclut uniquement les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName.|
|--log-level (chaîne)|Définit le niveau de détail pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes/réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie) (par défaut : « INFO »).|
|--put-md5|Crée un hachage MD5 de chaque fichier, puis enregistre le hachage en tant que propriété Content-MD5 de l’objet blob ou du fichier de destination (par défaut, le hachage n’est pas créé.) Disponible uniquement lors du chargement.|
|--recursive|True par défaut. Examine les sous-répertoires de manière récursive lors de la synchronisation des répertoires. (par défaut : true)|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
