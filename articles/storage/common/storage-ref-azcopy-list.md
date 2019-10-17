---
title: azcopy list | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513525"
---
# <a name="azcopy-list"></a>azcopy list

Liste les entités d’une ressource donnée.

## <a name="synopsis"></a>Synopsis

Seuls les conteneurs d’objets blob sont pris en charge dans la version actuelle.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Exemples

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Options

|Option|Description|
|--|--|
|-h, --help|Affiche l’aide de la commande list.|
|--machine-readable|Liste les tailles des fichiers en octets.|
|--mega-units|Affiche des unités de 1000, et non de 1024.|
|--running-tally|Calcule le nombre total de fichiers ainsi que leur taille.|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
