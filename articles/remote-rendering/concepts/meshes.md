---
title: Maillages
description: Définition des maillages dans l’étendue d’Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679375"
---
# <a name="meshes"></a>Maillages

## <a name="mesh-resource"></a>Ressource de maillage

Les maillages sont une [ressource partagée](../concepts/lifetime.md) non modifiable, qui peut uniquement être créée via une [conversion de modèle](../how-tos/conversion/model-conversion.md). Les maillages contiennent un ou plusieurs *sous-maillages*. Chaque sous-maillage fait référence à un [matériau](materials.md) avec lequel il doit être affiché par défaut. Pour placer un maillage dans l’espace 3D, ajoutez un [MeshComponent](#meshcomponent) à une [Entité](entities.md).

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

## <a name="next-steps"></a>Étapes suivantes

* [Matériaux](materials.md)
