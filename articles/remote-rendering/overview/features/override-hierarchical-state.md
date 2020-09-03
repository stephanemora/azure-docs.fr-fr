---
title: Remplacement d’état hiérarchique
description: Explique le concept de composants de remplacement d’état hiérarchique.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 99f57c212dfc44d84640224b1526ab770fe97230
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009455"
---
# <a name="hierarchical-state-override"></a>Remplacement d’état hiérarchique

Dans de nombreux cas, il est nécessaire de changer dynamiquement l’apparence de certaines parties d’un [modèle](../../concepts/models.md), par exemple, masquer des sous-graphes ou basculer vers un rendu transparent. Modifier un par un les éléments impliqués n’est pas pratique, car cela implique d’effectuer plusieurs itérations sur le graphe de toute la scène et de gérer le clonage et l’affectation de matériel sur chaque nœud.

Pour prendre en charge ce cas d’usage avec la surcharge la plus faible possible, utilisez `HierarchicalStateOverrideComponent`. Ce composant implémente des mises à jour d’état hiérarchiques sur des branches arbitraires du graphe de la scène. En d’autres termes, un état peut être défini à n’importe quel niveau du graphe de la scène ; il descend dans la hiérarchie jusqu’à ce qu’il soit remplacé par un nouvel état ou appliqué à un objet feuille.

À titre d’exemple, prenons le modèle d’une voiture : vous souhaitez rendre toute la voiture transparente, à l’exception de la partie moteur interne. Ce cas d’usage implique seulement deux instances du composant :

* Le premier composant est assigné au nœud racine du modèle et active le rendu transparent pour l’ensemble de la voiture.
* Le deuxième composant est affecté au nœud racine du moteur et remplace l’état en désactivant explicitement le mode semi-transparent.

## <a name="features"></a>Fonctionnalités

Voici l’ensemble fixe d’états qui peuvent être remplacés :

* **`Hidden`**  : Les maillages respectifs dans le graphe de la scène sont masqués ou affichés.
* **`Tint color`**  : Un objet rendu peut être teinté avec sa propre couleur et son propre poids de teinte. L’image ci-dessous illustre la teinte de couleur de la jante d’une roue.
  
  ![Teinte de couleur](./media/color-tint.png)

* **`See-through`**  : La géométrie est rendue de manière semi-transparente, par exemple pour révéler les parties internes d’un objet. L’image suivante illustre le rendu de la voiture tout entière en mode semi-transparent, à l’exception de l’étrier de frein rouge :

  ![Semi-transparent](./media/see-through.png)

  > [!IMPORTANT]
  > L’effet semi-transparent ne fonctionne qu’avec le [mode de rendu](../../concepts/rendering-modes.md) *TileBasedComposition*.

* **`Selected`**  : La géométrie est rendue avec un [contour de sélection](outlines.md).

  ![Contour de sélection](./media/selection-outline.png)

* **`DisableCollision`**  : La géométrie est exempte de [requêtes spatiales](spatial-queries.md). Comme l'indicateur **`Hidden`** n'affecte pas l'indicateur d'état des collisions, ces deux indicateurs sont souvent définis ensemble.

## <a name="hierarchical-overrides"></a>Remplacements hiérarchiques

`HierarchicalStateOverrideComponent` peut être attaché à plusieurs niveaux d’une hiérarchie d’objets. Étant donné qu’il ne peut y avoir qu’un seul composant de chaque type sur une entité, chaque `HierarchicalStateOverrideComponent` gère les états de masquage, semi-transparence, sélection, teinte de couleur et collision.

Chaque état peut donc avoir l’une des valeurs suivantes :

* `ForceOn` : l’état est activé pour tous les maillages sur et au-dessous de ce nœud.
* `ForceOff` : l’état est désactivé pour tous les maillages sur et au-dessous de ce nœud.
* `InheritFromParent` : l’état n’est pas affecté par ce composant de remplacement.

Il est possible de modifier les états directement ou à l’aide de la fonction `SetState` :

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Couleur de teinte

Le remplacement de `tint color` est légèrement spécial en ce sens qu'il existe à la fois un état activation/désactivation/héritage et une propriété de couleur de teinte. La partie alpha de la couleur de teinte définit le poids de l’effet de teinte : si la valeur est 0,0, aucune couleur de teinte n’est visible ; si la valeur est 1,0, l’objet est rendu avec une couleur de teinte pure. Pour les valeurs intermédiaires, la couleur finale est mélangée à la couleur de teinte. La couleur de teinte peut être modifiée image par image pour obtenir une animation de couleur.

## <a name="performance-considerations"></a>Considérations relatives aux performances

Une instance de `HierarchicalStateOverrideComponent` n’ajoute pas beaucoup de surcharge d’exécution en elle-même. Toutefois, il est toujours conseillé de limiter le nombre de composants actifs. Par exemple, pour implémenter un système de sélection qui met en surbrillance l’objet choisi, il est recommandé de supprimer le composant lorsque la mise en surbrillance est supprimée. Si les composants sont conservés avec des fonctionnalités neutres, le nombre de composants peut vite grimper.

Le rendu transparent place davantage de charge de travail sur les GPU du serveur que le rendu standard. Si une grande partie du graphe de la scène passe en mode *semi-transparent*, avec de nombreuses couches de géométrie visibles, il peut en résulter un goulot d’étranglement au niveau des performances. Il en est de même pour les objets avec [contours de sélection](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Étapes suivantes

* [Contours](../../overview/features/outlines.md)
* [Modes de rendu](../../concepts/rendering-modes.md)
* [Requêtes spatiales](../../overview/features/spatial-queries.md)
