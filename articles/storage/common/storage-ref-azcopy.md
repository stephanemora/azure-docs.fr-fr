---
title: azcopy | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 984d0c570c6c0d5048d58377f113319157411244
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513385"
---
# <a name="azcopy"></a>azcopy

AzCopy est un outil en ligne de commande qui permet de déplacer des données vers et à partir du stockage Azure.

## <a name="synopsis"></a>Synopsis

Le format général de la commande est le suivant : `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Pour signaler des problèmes ou pour en savoir plus sur l’outil, consultez [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="options"></a>Options

**--cap-mbps uint32**      Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.

**-h, --help** Aide pour azcopy
      
**--output-type**  Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ». (par défaut : « text »).

## <a name="see-also"></a>Voir aussi

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [azcopy copy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy jobs](storage-ref-azcopy-jobs.md)
- [azcopy list](storage-ref-azcopy-list.md)
- [azcopy login](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy remove](storage-ref-azcopy-remove.md)
- [azcopy sync](storage-ref-azcopy-sync.md)
