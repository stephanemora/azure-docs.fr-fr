---
title: Étiquettes de contenu - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés à la fonction de balises d’images de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ad01751f7f7c573352c8fd15cc0f8cc2ebbbe700
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945101"
---
# <a name="applying-content-tags-to-images"></a>Application d’étiquettes de contenu aux images

Vision par ordinateur retourne des étiquettes basées sur des milliers d’objets, d’êtres vivants, de scènes et d’actions reconnaissables. Lorsque les étiquettes sont ambigües ou inhabituelles, la réponse de l’API fournit des « conseils » pour expliquer la signification de l’étiquette dans le contexte des paramètres connus. Les étiquettes ne sont pas organisées sous forme de taxonomie et aucune hiérarchie d’héritage n’existe. La collection d’étiquettes de contenu constitue la « description » essentielle d’une image, qui est affichée sous forme de texte lisible par l’homme (phrases complètes). Notez que, à ce stade, seul l’anglais est pris en charge pour la description d’images.

Après le chargement d’une image ou d’une URL d’image, les algorithmes de l’API Vision par ordinateur génèrent des étiquettes basées sur les objets, les êtres vivants et les actions identifiées dans l’image. L’étiquetage ne se limite pas au sujet principal, comme une personne au premier plan, mais il inclut également le décor (intérieur ou extérieur), le mobilier, les outils, les plantes, les animaux, les accessoires, les gadgets, etc.

## <a name="image-tagging-example"></a>Exemple d’étiquetage d’images

Les réponses JSON suivantes illustrent ce que renvoie Vision par ordinateur au moment de l’étiquetage d’éléments visuels détectés dans l’image en exemple.

![Maison bleu et jardin en façade](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de [classement d’images](concept-categorizing-images.md) et de [description des images](concept-describing-images.md).
