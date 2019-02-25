---
title: Classement des images - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés à la fonction de catégorisation d’images de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a85fb65ad971bfc70bdab0689e99921e616b56ac
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308857"
---
# <a name="categorize-images"></a>Classer des images

Outre les balises et les descriptions, Vision par ordinateur renvoie des catégories basées sur la taxonomie détectées dans une image. Contrairement aux balises, les catégories sont organisées selon une hiérarchie héréditaire parent/enfant, et elles sont moins nombreuses (86 contre des milliers de balises). Tous les noms de catégorie sont en anglais. La catégorisation peut se faire par elle-même ou à côté du modèle de balises plus récentes.

## <a name="the-86-category-concept"></a>Concept des 86 catégories

Vision par ordinateur peut catégoriser une image de manière large ou spécifique, en utilisant la liste de 86 catégories dans le diagramme suivant. Pour consulter la taxonomie complète au format texte, consultez [Taxonomie des catégories](category-taxonomy.md).

![Listes groupées de toutes les catégories de la taxonomie des catégories](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exemples de classement d’images

Les réponses JSON suivantes illustrent ce que renvoie Vision par ordinateur au moment de classer l’image fournie à titre d’exemple d’après ses fonctionnalités visuelles.

![Femme sur le toit d’un immeuble d’appartements](./Images/woman_roof.png)

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
| ![Quatre personnes réunies comme une famille](./Images/family_photo.png) | people_group |
| ![Chiot assis dans un champ herbeux](./Images/cute_dog.png) | animal_dog |
| ![Personne se tenant sur un rocher montagneux au coucher du soleil](./Images/mountain_vista.png) | outdoor_mountain |
| ![Pile de petits pains sur une table](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de [balisage d’images](concept-tagging-images.md) et de [classement des images](concept-describing-images.md).
