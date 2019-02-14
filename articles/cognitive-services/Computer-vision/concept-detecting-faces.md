---
title: Détection des visages - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la fonctionnalité de détection des visages de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 81e846c6ffc0361b7154c962dbc047b9e5ae55e7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872983"
---
# <a name="face-detection-with-computer-vision"></a>Détection des visages avec Vision par ordinateur

Vision par ordinateur détecte les visages au sein d’une image et génère l’âge, le sexe et le rectangle du visage de chaque visage détecté. Il fournit un sous-ensemble de fonctionnalités proposées par le service [Visage](/azure/cognitive-services/face/). Vous pouvez également utiliser le service Visage pour effectuer une analyse plus détaillée, comme l’identification faciale et la détection de la pose.  

## <a name="face-detection-examples"></a>Exemples de détection des visages

Le premier exemple montre la réponse JSON retournée par Vision par ordinateur pour une image contenant un seul visage.

![Analyse Vision femme toit visage](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

Le deuxième exemple montre la réponse JSON retournée pour une image contenant plusieurs visages.

![Analyse Vision photo de famille visage](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts concernant la [détection du contenu spécifique à un domaine](concept-detecting-domain-content.md).
