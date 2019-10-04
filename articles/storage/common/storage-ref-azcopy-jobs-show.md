---
title: azcopy jobs show | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196811"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Affiche des informations détaillées sur l’ID d’un travail donné.

## <a name="synopsis"></a>Synopsis

Si l’ID du travail est fourni seul et sans indicateur, le résumé de progression du travail est retourné.

Si l’indicateur `with-status` est défini, la liste des transferts du travail avec la valeur donnée est affichée.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Options

|Option|Description|
|--|--|
|-h, --help|Affiche l’aide de la commande show.|
|--with-status (chaîne)|Liste uniquement les transferts du travail avec cet état. Valeurs possibles : Started, Success, Failed|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy jobs](storage-ref-azcopy-jobs.md)
