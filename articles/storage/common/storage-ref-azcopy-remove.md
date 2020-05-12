---
title: azcopy remove | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f76489f384f233f65eb8fcca3a8359cd5b67c20a
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780723"
---
# <a name="azcopy-remove"></a>azcopy remove

Supprimez des objets BLOB ou des fichiers d’un compte de stockage Azure.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Articles conceptuels associés

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)
- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)
- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemples

Supprimer un objet blob avec l’authentification SAP :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Supprimer l’intégralité d’un répertoire virtuel avec l’authentification SAP :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Supprimer uniquement les principaux objets blob d’un répertoire virtuel, mais pas ses sous-répertoires :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Supprimer un sous-ensemble d’objets blob dans un répertoire virtuel (par exemple, uniquement les fichiers .jpg et PDF, ou uniquement les objets blob dont le nom est « exactName ») :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Supprimer l’intégralité d’un répertoire virtuel, mais exclure certains objets blob (par exemple, tous ceux dont le nom commence par « foo » ou se termine par « bar ») :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Supprimez des objets BLOB et des répertoires virtuels spécifiques en plaçant leurs chemins d’accès relatifs (NON codés URL) dans un fichier :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Supprimez un seul fichier d’un compte de stockage d’objets BLOB qui a un espace de noms hiérarchique (inclure/exclure non pris en charge).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Supprimez un seul répertoire d’un compte de stockage d’objets BLOB qui a un espace de noms hiérarchique (inclure/exclure non pris en charge) :

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Options

Chaîne **--delete-snapshots**   Par défaut, l’opération de suppression échoue si un objet blob comporte des captures instantanées. Spécifiez « include » pour supprimer l’objet blob racine et tous ses instantanés. Vous pouvez également spécifier « only » pour supprimer uniquement les captures instantanées et conserver l’objet blob racine.

Chaîne **--exclude-path**      Exclut ces chemins d’accès lors de la suppression. Cette option ne prend pas en charge les caractères génériques (*). Vérifie le préfixe du chemin d’accès relatif. Par exemple : myFolder;myFolder/subDirName/file.pdf.

Chaîne **--exclude-pattern** Exclut les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName

**--force-if-read-only**    Lors de la suppression d’un fichier ou d’un dossier Azure Files, force la suppression même si l’objet existant a son attribut en lecture seule défini.

**-h, --help**   Aide pour la suppression.

Chaîne **--include-path**      Inclut uniquement ces chemins lors de la suppression. Cette option ne prend pas en charge les caractères génériques (*). Vérifie le préfixe du chemin d’accès relatif. Par exemple : myFolder;myFolder/subDirName/file.pdf

Chaîne **--include-pattern** Inclut uniquement les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName

Chaîne **--list-of-files**    Définit l’emplacement d’un fichier qui contient la liste des fichiers et répertoires à supprimer. Les chemins d’accès relatifs doivent être délimités par des sauts de ligne, et les chemins d’accès ne doivent pas être codés URL.

Chaîne **--log-level**         Définit la verbosité du journal pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes/réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie) (« INFO » par défaut) (« INFO » par défaut)

**--recursive**                Examine les sous-répertoires de manière récursive lors de la synchronisation des répertoires.

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|
|--trusted-microsoft-suffixes (chaîne)   | Spécifie des suffixes de domaine supplémentaires où des jetons de connexion Azure Active Directory peuvent être envoyés.  La valeur par défaut est «  *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net ». Tous les éléments répertoriés ici sont ajoutés à la valeur par défaut. Pour la sécurité, vous devez placer uniquement des domaines Microsoft Azure ici. Séparez plusieurs entrées par des points-virgules.|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
