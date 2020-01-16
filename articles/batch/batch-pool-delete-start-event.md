---
title: Événement de début de suppression de pool Azure Batch | Microsoft Docs
description: Référence pour l’événement de début de suppression de pool Batch.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: d582e2b04d203484632a1781d1819f612de41fe7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026729"
---
# <a name="pool-delete-start-event"></a>Événement de début de suppression de pool

 Cet événement est émis quand une opération de suppression du pool a commencé. Étant donné que la suppression de pool est un événement asynchrone, vous pouvez vous attendre à ce qu’un événement de fin de suppression de pool soit émis au terme de l’opération de suppression.

 L’exemple suivant montre le corps d’un événement de début de suppression de pool.

```
{
    "id": "myPool1"
}
```

|Élément|Type|Notes|
|-------------|----------|-----------|
|`id`|String|ID du pool.|
