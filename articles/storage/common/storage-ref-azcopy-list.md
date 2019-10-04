---
title: azcopy list | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196807"
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
