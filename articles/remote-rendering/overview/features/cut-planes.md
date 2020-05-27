---
title: Plans de coupe
description: Explique ce que sont les plans de coupe et comment les utiliser
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 5d641b573a1cad5cac6db6199f5bad5c06151c62
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759079"
---
# <a name="cut-planes"></a>Plans de coupe

Un *plan de coupe* est une fonctionnalité visuelle qui découpe les pixels d’un côté d’un plan virtuel, et révèle l’intérieur des [maillages](../../concepts/meshes.md).
L’image ci-dessous illustre l’effet. La partie gauche affiche le maillage d’origine, celle de droite permet de regarder à l’intérieur du maillage :

![Plan de coupe](./media/cutplane-1.png)

## <a name="limitations"></a>Limites

* Pour le moment, Azure Remote Rendering prend en charge un **nombre maximal de huit plans de coupe actifs**. Vous pouvez créer des composants de plan de coupe supplémentaires, mais si vous essayez d’en activer davantage simultanément, l’activation est ignorée. Désactivez d’abord d’autres plans si vous souhaitez changer le composant qui doit modifier la scène.
* Chaque plan de coupe a une incidence sur tous les objets rendus à distance. Il n’existe actuellement aucun moyen d’exclure des objets ou des zones de maillage spécifiques.
* Les plans de coupe sont uniquement une fonctionnalité visuelle, ils n’affectent pas le résultat des [requêtes spatiales](spatial-queries.md). Si vous ne souhaitez pas utiliser le ray casting sur un maillage ouvert en coupe, vous pouvez ajuster le point de départ du rayon pour qu’il se trouve sur le plan de coupe. De cette façon, le rayon ne peut atteindre que les parties visibles.

## <a name="performance-considerations"></a>Considérations relatives aux performances

Chaque plan de coupe actif entraîne une faible dépense pendant le rendu. Désactivez ou supprimez les plans de coupe lorsqu’ils ne sont pas nécessaires.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Vous ajoutez un plan de coupe à la scène en créant un élément *CutPlaneComponent*. L’emplacement et l’orientation du plan sont déterminés par l’[entité](../../concepts/entities.md) propriétaire du composant.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>Propriétés CutPlaneComponent

Les propriétés suivantes sont exposées sur un composant de plan de coupe :

* **Activée :** vous pouvez désactiver temporairement les plans de coupe en désactivant le composant. Les plans de coupe désactivés n’entraînent pas de surcharge de rendu, et ne sont pas comptabilisés dans la limite globale des plans de coupe.

* **Normal :** spécifie la direction (+X,-X,+Y,-Y,+Z,-Z) qui est utilisée comme plan perpendiculaire. Cette direction est relative à l’orientation de l’entité propriétaire. Déplacez et faites pivoter l’entité propriétaire pour obtenir un positionnement précis.

* **FadeColor** et **FadeLength :**

  si la valeur alpha de *FadeColor* est différente de zéro, les pixels proches du plan de coupe s’estompent vers la partie RVB de FadeColor. La puissance du canal alpha détermine s’il s’estompe entièrement dans la couleur du fondu, ou seulement partiellement. *FadeLength* définit la distance au-delà de laquelle ce fondu doit avoir lieu.

## <a name="next-steps"></a>Étapes suivantes

* [Rendu unilatéral](single-sided-rendering.md)
* [Requêtes spatiales](spatial-queries.md)
