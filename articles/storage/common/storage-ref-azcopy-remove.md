---
title: azcopy remove | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196795"
---
# <a name="azcopy-remove"></a>azcopy remove

Supprime les entités d’Azure Storage Blob, d’Azure Files et d’Azure Data Lake Storage Gen2.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

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

Supprimer un fichier de Data Lake Storage Gen2 (include et exclude ne sont pas pris en charge) :

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Supprimer un répertoire de Data Lake Storage Gen2 (include et exclude ne sont pas pris en charge) :

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Options

|Option|Description|
|--|--|
|--exclude (chaîne)|Exclut les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName|
|-h, --help|Affiche l’aide de la commande remove.|
|--include (chaîne)|Inclure uniquement les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName|
|--log-level (chaîne)|Définit le niveau de détail pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes/réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie) (par défaut : « INFO »)|
|--recursive|Examine les sous-répertoires de manière récursive lors de la synchronisation des répertoires.|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
