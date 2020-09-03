---
title: Limites des objets
description: Explique comment interroger les limites des objets spatiaux
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 099b0175b906e48034e663b575517e49bce7e753
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021848"
---
# <a name="object-bounds"></a>Limites des objets

Les limites des objets représentent le volume occupé par une [entité](entities.md) et ses enfants. Dans Azure Remote Rendering, les limites des objets sont toujours fournies sous la forme de *cadres englobants alignés aux axes* (AABB, axis aligned bounding box). Les limites des objets peuvent être dans un *espace local* ou un *espace universel*. Dans les deux cas, elles sont toujours alignées sur des axes. Leurs étendues et leur volume peuvent donc varier entre la représentation dans l’espace local et dans l’espace universel.

## <a name="querying-object-bounds"></a>Interrogation des limites des objets

L’AABB local d’un [maillage](meshes.md) peut être interrogé directement à partir de la ressource de maillage. Ces limites peuvent être transformées dans l’espace local ou dans l’espace universel d’une entité à l’aide de la transformation de l’entité.

Il est possible de calculer les limites d’une hiérarchie d’objets entière de cette manière, mais cela nécessite de parcourir la hiérarchie, d’interroger les limites pour chaque maillage et de les combiner manuellement. Cette opération est fastidieuse et inefficace.

Il est plus avantageux d’appeler `QueryLocalBoundsAsync` ou `QueryWorldBoundsAsync` sur une entité. Le calcul est ensuite déplacé sur le serveur et retourné sous un délai minime.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Requêtes spatiales](../overview/features/spatial-queries.md)
