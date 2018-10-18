---
title: Étiquetage des images - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à l’étiquetage des images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 159a1b9404f6ce635dcc8bb6007e52b7346ef98d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982962"
---
# <a name="tagging-images"></a>Étiquetage des images

L’API Vision par ordinateur renvoie des balises basées sur plus de 2000 objets, êtres vivants, scènes et actions reconnaissables. Lorsque les balises sont ambigües ou inhabituelles, la réponse de l’API fournit des « conseils » pour expliquer la signification de la balise dans le contexte des paramètres connus. Les balises ne sont pas organisées sous forme de taxonomie et aucune hiérarchie d’héritage n’existe. La collection de balises de contenu constitue la « description » essentielle d’une image, qui est affichée sous forme de texte lisible par l’homme (phrases complètes). Notez que, à ce stade, seul l’anglais est pris en charge pour la description d’images.

Après le chargement d’une image ou d’une URL d’image, les algorithmes de l’API Vision par ordinateur génèrent des balises basées sur les objets, les êtres vivants et les actions identifiées dans l’image. Le balisage ne se limite pas au sujet principal, comme une personne au premier plan, mais il inclut également le décor (intérieur ou extérieur), le mobilier, les outils, les plantes, les animaux, les accessoires, les gadgets, etc.

## <a name="image-tagging-example"></a>Exemple de balisage d’images

Les réponses JSON suivantes illustrent ce que renvoie Vision par ordinateur au moment du balisage d’éléments visuels détectés dans l’image en exemple.

![House_Yard](./Images/house_yard.png).

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