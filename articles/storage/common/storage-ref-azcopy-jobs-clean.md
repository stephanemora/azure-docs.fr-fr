---
title: azcopy jobs clean | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518189"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Supprimer tous les fichiers journaux et fichiers de plan pour tous les travaux

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>Exemples

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Options

**-h, --help**                                 Aide pour le nettoyage.

**--with-status** string   Supprime uniquement les tâches avec cet état ; valeurs disponibles : Canceled, Completed, Failed, InProgress, All (par défaut : « All »)

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

**--cap-mbps uint32**      Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.

**--output-type** string   Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ». (par défaut : « text »).

## <a name="see-also"></a>Voir aussi

- [azcopy jobs](storage-ref-azcopy-jobs.md)
