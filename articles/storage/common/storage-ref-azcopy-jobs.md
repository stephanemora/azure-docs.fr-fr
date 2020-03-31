---
title: azcopy jobs | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 27c06656d95c5165b33b6056a3cf3b554f0e5469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034110"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Sous-commandes relatives à la gestion des travaux.

## <a name="related-conceptual-articles"></a>Articles conceptuels associés

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)
- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)
- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemples

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Options

|Option|Description|
|--|--|
|-h, --help|Affiche l’aide de la commande jobs.|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
