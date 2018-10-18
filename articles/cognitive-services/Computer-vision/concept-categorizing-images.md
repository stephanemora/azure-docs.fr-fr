---
title: Classement des images - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés au classement des images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 602ea8028cf89b23df692d5c2fb9b781f64bcad4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341682"
---
# <a name="categorizing-images"></a>Classement des images

Outre le balisage et les descriptions, l’API Vision par ordinateur renvoie des catégories basées sur la taxonomie telles que définies dans les versions précédentes. Ces catégories sont classées au sein d’une taxonomie comprenant des hiérarchies héréditaire parent/enfant. Toutes les catégories sont en anglais. Les catégories peuvent être utilisées en tant que telles ou avec nos nouveaux modèles de balisage.

## <a name="the-86-category-concept"></a>Concept des 86 catégories

Selon une liste de 86 concepts (voir le diagramme ci-dessous), une image peut être classée, qu’elle soit générale ou spécifique. Pour consulter la taxonomie complète au format texte, consultez [Taxonomie des catégories](category-taxonomy.md).

![Analyser les catégories](./Images/analyze_categories.png)

## <a name="image-categorization-examples"></a>Exemples de classement d’images

Les réponses JSON suivantes illustrent ce que renvoie Vision par ordinateur au moment de classer l’image fournie à titre d’exemple d’après ses fonctionnalités visuelles.

![Toit femme](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

Le tableau suivant illustre un ensemble d’images type et la catégorie renvoyée par Vision par ordinateur pour chacune d’entre elles.

| Image | Catégorie |
|-------|----------|
| ![Photo de famille](./Images/family_photo.png) | people_group |
| ![Chien mignon](./Images/cute_dog.png) | animal_dog |
| ![Montagne extérieur](./Images/mountain_vista.png) | outdoor_mountain |
| ![Analyse Vision aliment pain](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de [balisage d’images](concept-tagging-images.md) et de [classement des images](concept-describing-images.md).