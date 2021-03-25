---
title: Événement de début de suppression de pool Azure Batch
description: Référence pour l’événement de début de suppression de pool Batch. Cet événement est émis quand une opération de suppression du pool a commencé.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803746"
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
