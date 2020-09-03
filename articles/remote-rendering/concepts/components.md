---
title: Components
description: Définition des composants dans l’étendue d’Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: d957da572bfdd3119dda506ac8f5bb42064d7758
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020301"
---
# <a name="components"></a>Components

Azure Remote Rendering utilise le modèle de [système de composants d’entité](https://en.wikipedia.org/wiki/Entity_component_system). Alors que les [entités](entities.md) représentent la position et la composition hiérarchique des objets, les composants sont responsables de l’implémentation du comportement.

Les types de composants les plus fréquemment utilisés sont [:::no-loc text="mesh components":::](meshes.md), qui ajoutent des maillages au pipeline de rendu. De même, des [composants légers](../overview/features/lights.md) sont utilisés pour ajouter de l’éclairage et des [composants de plan de coupe](../overview/features/cut-planes.md) pour couper les maillages ouverts.

Tous ces composants utilisent comme point de référence la transformation (position, rotation, échelle) de l’entité à laquelle ils sont attachés.

## <a name="working-with-components"></a>Utilisation des composants

Vous pouvez facilement ajouter, supprimer et manipuler des composants par programme :

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```


Un composant est attaché à une entité au moment de la création. Il n’est pas possible de le déplacer vers une autre entité par la suite. Les composants sont supprimés explicitement avec `Component.Destroy()` ou automatiquement lors de la destruction de l’entité propriétaire du composant.

Il n’est possible d’ajouter à une entité qu’une seule instance de chaque type de composant à la fois.

## <a name="unity-specific"></a>Spécificité d’Unity

L’intégration d’Unity a des fonctions d’extension supplémentaires pour interagir avec les composants. Consultez [Composants et objets de jeu Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Étapes suivantes

* [Limites des objets](object-bounds.md)
* [Maillages](meshes.md)
