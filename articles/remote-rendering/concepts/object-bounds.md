---
title: Limites des objets
description: Explique comment interroger les limites des objets spatiaux
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594482"
---
# <a name="object-bounds"></a>Limites des objets

Les limites des objets représentent le volume occupé par une [entité](entities.md) et ses enfants. Dans Azure Remote Rendering, les limites des objets sont toujours fournies sous la forme de *cadres englobants alignés aux axes* (AABB, axis aligned bounding box). Les limites des objets peuvent être dans un *espace local* ou un *espace universel*. Dans les deux cas, elles sont toujours alignées sur des axes. Leurs étendues et leur volume peuvent donc varier entre la représentation dans l’espace local et dans l’espace universel.

## <a name="querying-object-bounds"></a>Interrogation des limites des objets

Le cadre englobant local aligné aux axes d’un [maillage](meshes.md) peut être interrogé directement à partir de la ressource de maillage. Ces limites peuvent être transformées dans l’espace local ou dans l’espace universel d’une entité à l’aide de la transformation de l’entité.

Il est possible de calculer les limites d’une hiérarchie d’objets entière de cette manière, mais cela nécessite de parcourir la hiérarchie, d’interroger les limites pour chaque maillage et de les combiner manuellement. Cette opération est fastidieuse et inefficace.

Il est plus avantageux d’appeler `QueryLocalBoundsAsync` ou `QueryWorldBoundsAsync` sur une entité. Le calcul est ensuite déplacé sur le serveur et retourné sous un délai minime.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>Documentation de l’API

* [Entity.QueryLocalBoundsAsync, C#](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entity::QueryLocalBoundsAsync, C++](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Étapes suivantes

* [Requêtes spatiales](../overview/features/spatial-queries.md)