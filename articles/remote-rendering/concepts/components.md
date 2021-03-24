---
title: Components
description: Définition des composants dans l’étendue d’Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592192"
---
# <a name="components"></a>Components

Azure Remote Rendering utilise le modèle de [système de composants d’entité](https://en.wikipedia.org/wiki/Entity_component_system). Alors que les [entités](entities.md) représentent la position et la composition hiérarchique des objets, les composants sont responsables de l’implémentation du comportement.

Les types de composants les plus fréquemment utilisés sont [:::no-loc text="mesh components":::](meshes.md), qui ajoutent des maillages au pipeline de rendu. De même, des [composants légers](../overview/features/lights.md) sont utilisés pour ajouter de l’éclairage et des [composants de plan de coupe](../overview/features/cut-planes.md) pour couper les maillages ouverts.

Tous ces composants utilisent comme point de référence la transformation (position, rotation, échelle) de l’entité à laquelle ils sont attachés.

## <a name="working-with-components"></a>Utilisation des composants

Vous pouvez facilement ajouter, supprimer et manipuler des composants par programme :

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Un composant est attaché à une entité au moment de la création. Il n’est pas possible de le déplacer vers une autre entité par la suite. Les composants sont supprimés explicitement avec `Component.Destroy()` ou automatiquement lors de la destruction de l’entité propriétaire du composant.

Il n’est possible d’ajouter à une entité qu’une seule instance de chaque type de composant à la fois.

## <a name="unity-specific"></a>Spécificité d’Unity

L’intégration d’Unity a des fonctions d’extension supplémentaires pour interagir avec les composants. Consultez [Composants et objets de jeu Unity](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>Documentation de l’API

* [ComponentBase, C#](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection.CreateComponent()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [Entity.FindComponentOfType(), C#](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [ComponentBase, C++](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection::CreateComponent()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [Entity::FindComponentOfType(), C++](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Étapes suivantes

* [Limites des objets](object-bounds.md)
* [Maillages](meshes.md)