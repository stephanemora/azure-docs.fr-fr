---
title: Rendu unilatéral
description: Décrit les paramètres et les cas d'usage du rendu unilatéral
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 97e0456e274adee7d678e373cfd92b5003f3d801
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759096"
---
# <a name="single-sided-rendering"></a>Rendu unilatéral

La plupart des renderers ont recours à l'[élimination des faces arrière](https://en.wikipedia.org/wiki/Back-face_culling) pour améliorer les performances. Pourtant, lorsque les maillages sont ouverts avec des [plans de coupe](cut-planes.md), les utilisateurs regardent souvent la face arrière des triangles. Si ces triangles sont éliminés, le résultat n'est pas convaincant.

Pour contourner ce problème, la solution consiste à appliquer aux triangles un rendu *double face*. Comme l'absence d'élimination des faces arrière se répercute sur les performances, par défaut Azure Remote Rendering applique uniquement le rendu double face aux maillages qui croisent un plan de coupe.

Le paramètre de *rendu unilatéral* vous permet de personnaliser ce comportement.

> [!CAUTION]
> Le paramètre de rendu unilatéral est une fonctionnalité expérimentale. Il pourrait être supprimé à l'avenir. Ne modifiez pas le paramètre par défaut, sauf si cela résout un problème critique dans votre application.

## <a name="prerequisites"></a>Prérequis

Le paramètre de rendu unilatéral n'a d'effet que sur les maillages qui ont été [convertis](../../how-tos/conversion/configure-model-conversion.md) avec l'option `opaqueMaterialDefaultSidedness` définie sur `SingleSided`. Par défaut, la valeur attribuée à cette option est `DoubleSided`.

## <a name="single-sided-rendering-setting"></a>Paramètre de rendu unilatéral

Trois modes sont disponibles :

**Normal :** dans ce mode, les maillages sont toujours rendus tels qu'ils sont convertis. Autrement dit, les maillages convertis avec l'option `opaqueMaterialDefaultSidedness` définie sur `SingleSided` sont toujours rendus avec l'élimination des faces arrière activée, même lorsqu'ils croisent un plan de coupe.

**DynamicDoubleSiding :** dans ce mode, lorsqu'un plan de coupe croise un maillage, il est automatiquement remplacé par un rendu double face. Il s'agit du mode par défaut.

**AlwaysDoubleSided :** force à tout moment un rendu double face pour les géométries unilatérales. Ce mode est en grande partie exposé pour vous permettre de comparer facilement l'impact sur les performances d'un rendu unilatéral et d'un rendu double face.

Pour modifier les paramètres de rendu unilatéral, procédez comme suit :

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Plans de coupe](cut-planes.md)
* [Configurer la conversion de modèle](../../how-tos/conversion/configure-model-conversion.md)
