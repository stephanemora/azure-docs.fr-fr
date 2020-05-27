---
title: Événement de début de suppression de pool Azure Batch
description: Référence pour l’événement de début de suppression de pool Batch. Cet événement est émis quand une opération de suppression du pool a commencé.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723933"
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
