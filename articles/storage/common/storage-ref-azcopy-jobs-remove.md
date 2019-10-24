---
title: azcopy jobs remove | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518121"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Supprime tous les fichiers associés à l’ID de travail donné.

> [!NOTE] 
> Vous pouvez personnaliser l’emplacement où sont enregistrés les fichiers journaux et les fichiers de plan. Pour plus d’informations, consultez la commande [azcopy env](storage-ref-azcopy-env.md).

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>Exemples

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Options

**-h, --help**                     Aide pour la suppression.

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

**--cap-mbps uint32**      Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.

**--output-type** string   Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ». (par défaut : « text »).

## <a name="see-also"></a>Voir aussi

- [azcopy jobs](storage-ref-azcopy-jobs.md)
