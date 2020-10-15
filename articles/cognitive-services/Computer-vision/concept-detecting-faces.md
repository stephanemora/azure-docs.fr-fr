---
title: Détection des visages - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés à la fonctionnalité Détection de visage de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d10e4c98d3e2f00e7139063c8263e65ffd015d7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244698"
---
# <a name="face-detection-with-computer-vision"></a>Détection des visages avec Vision par ordinateur

Vision par ordinateur permet de détecter les visages au sein d’une image et génère l’âge, le sexe et le rectangle du visage de chaque visage détecté. 

> [!NOTE]
> Cette fonctionnalité est également proposée par le service [Visage](/azure/cognitive-services/face/) Azure. Consultez cette alternative pour une analyste plus détaillée des visages, avec identification faciale et détection de la pose. 

## <a name="face-detection-examples"></a>Exemples de détection des visages

L'exemple suivant montre la réponse JSON renvoyée par Vision par ordinateur pour une image contenant un seul visage.

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

L'exemple suivant montre la réponse JSON renvoyée pour une image contenant plusieurs visages.

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

## <a name="use-the-api"></a>Utilisation de l’API

La fonctionnalité de détection des visages fait partie de l’API [Analyser l’image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). Vous pouvez appeler cette API via un SDK natif ou via des appels REST. Incluez `Faces` dans le paramètre de requête **visualFeatures**. Ensuite, lorsque vous obtenez la réponse JSON complète, analysez simplement la chaîne de contenu de la section `"faces"`.

* [Démarrage rapide : SDK .NET Vision par ordinateur](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Démarrage rapide : Analyser une image (API REST)](./quickstarts/csharp-analyze.md)
