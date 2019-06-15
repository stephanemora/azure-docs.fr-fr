---
title: Événement de fin de suppression de pool Azure Batch | Microsoft Docs
description: Référence pour l’événement de fin de suppression de pool Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775761"
---
# <a name="pool-delete-complete-event"></a>Événement de fin de suppression de pool

 Cet événement est émis quand une opération de suppression de pool est terminée.

 L’exemple suivant montre le corps d’un événement de fin de suppression de pool.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Élément|Type|Notes|
|-------------|----------|-----------|
|id|Chaîne|ID du pool.|
|startTime|Datetime|Heure de début de la suppression du pool.|
|endTime|DateTime|Heure de fin de la suppression du pool.|

## <a name="remarks"></a>Remarques
Pour plus d’informations sur les états et les codes d’erreur pour l’opération de redimensionnement de pool, voir [Supprimer un pool d’un compte](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).