---
title: Détection des types d’images - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la détection des types d’images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: ecdbdd60af41ee14070f2f45dfe50875e38ac3b3
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341997"
---
# <a name="detecting-image-types"></a>Détection des types d’images

Vision par ordinateur analyse le type de contenu des images, indique si une image est de type clipart, puis évalue la probabilité qu’il s’agisse d’un dessin à l’échelle ou d’un dessin au trait.

## <a name="detecting-clip-art"></a>Détection des images clipart

Vision par ordinateur analyse une image et évalue sa probabilité d’être une image clipart sur une échelle de 0 à 3, comme décrit dans le tableau suivant.

| Valeur | Signification |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Ambigu |
| 2 | Image clipart normale |
| 3 | 3 Image clipart de bonne qualité |

### <a name="clip-art-detection-examples"></a>Exemples de détection des images clipart

Les réponses JSON suivantes illustrent ce que renvoie Vision par ordinateur au moment d’évaluer la probabilité pour des images d’être des images clipart.

![Analyse Vision clipart fromage](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Analyse Vision jardin maison](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Détection des dessins au trait

Vision par ordinateur analyse une image et renvoie une valeur booléenne indiquant si l’image est un dessin au trait.

### <a name="line-drawing-detection-examples"></a>Exemples de détection de dessin au trait

Les réponses JSON suivantes illustrent ce que Vision par ordinateur renvoie si les images fournies à titre d’exemple sont des dessins au trait.

![Analyse Vision dessin lion](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Analyse Vision fleur](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de [balisage d’images](concept-tagging-images.md) et de [catégorisation des images](concept-categorizing-images.md).