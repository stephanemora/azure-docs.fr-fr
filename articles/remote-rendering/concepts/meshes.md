---
title: Maillages
description: Définition des maillages dans l’étendue d’Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594516"
---
# <a name="meshes"></a>Maillages

## <a name="mesh-resource"></a>Ressource de maillage

Les maillages sont une [ressource partagée](../concepts/lifetime.md) non modifiable, qui peut uniquement être créée via une [conversion de modèle](../how-tos/conversion/model-conversion.md). Les maillages contiennent un ou plusieurs *sous-maillages* avec une représentation physique pour les [requêtes raycast](../overview/features/spatial-queries.md). Chaque sous-maillage fait référence à un [matériau](materials.md) avec lequel il doit être affiché par défaut. Pour placer un maillage dans l’espace 3D, ajoutez un [MeshComponent](#meshcomponent) à une [Entité](entities.md).

### <a name="mesh-resource-properties"></a>Propriétés des ressources de maillage

Les propriétés de la classe `Mesh` sont les suivantes :

* **Materials :** tableau de matériaux. Chaque matériau est utilisé par un sous-maillage différent. Plusieurs entrées dans le tableau peuvent faire référence au même [matériau](materials.md). Ces données ne peuvent pas être modifiées au moment de l’exécution.

* **Bounds :** niveau du bit aligné sur l’axe spatial local (AABB) des vertex de maillage.

## <a name="meshcomponent"></a>MeshComponent

La classe `MeshComponent` est utilisée pour placer l’instance d’une ressource de maillage. Chaque MeshComponent fait référence à un maillage unique. Elle peut remplacer les matériaux utilisés pour afficher chaque sous-maille.

### <a name="meshcomponent-properties"></a>Propriétés de la classe MeshComponent

* **Mesh :** ressource de maillage utilisée par ce composant.

* **Materials :** tableau de matériaux spécifiés sur le composant de maillage lui-même. Le tableau aura toujours la même longueur que le tableau *Matériaux* sur la ressource de maillage. Les matériaux dont le maillage par défaut ne doit pas être substitué sont définis sur *Null* dans ce tableau.

* **UsedMaterials :** tableau des matériaux réellement utilisés pour chaque sous-maille. Sera identique aux données du tableau *Matériaux* pour les valeurs non Null. Dans le cas contraire, il contient la valeur du tableau *Matériaux* de l’instance de maille.

### <a name="sharing-of-meshes"></a>Partage de maillages

Une ressource `Mesh` peut être partagée entre plusieurs instances de composants de maillage. Par ailleurs, la ressource `Mesh` qui est assignée à un composant de maillage peut à tout moment être changée par programmation. Le code ci-dessous montre comment cloner un maillage :

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>Documentation de l’API

* [Mesh, classe C#](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [MeshComponent, classe C#](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Mesh, classe C++](/cpp/api/remote-rendering/mesh)
* [MeshComponent, classe C++](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Étapes suivantes

* [Matériaux](materials.md)