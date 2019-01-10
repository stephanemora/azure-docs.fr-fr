---
title: 'Détection des jeux de couleurs : Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Concepts liés à la détection des jeux de couleurs dans les images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 90eacdb120603fdb68c0f6c3de3457964ec5fd75
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579360"
---
# <a name="detect-color-schemes-in-images"></a>Détecter les jeux de couleurs dans les images

Vision par ordinateur extrait les couleurs d’une image. Les couleurs sont ensuite analysées dans trois contextes différents : la couleur de premier plan dominante, la couleur d’arrière-plan dominante et les couleurs dominantes de l’image dans sa globalité. Elles sont regroupées en 12 couleurs d’accentuation dominantes. Les couleurs d’accentuation sont les suivantes : noir, bleu, marron, gris, vert, orange, rose, violet, rouge, vert foncé, blanc et jaune. Vision par ordinateur analyse les couleurs extraites à partir d’une image afin de retourner une couleur d’accentuation qui représente la couleur la plus vive de l’image vers les visionneuses, grâce à une combinaison de couleurs dominantes et de saturation. Selon les images, qu’elles soient simplement en noir et blanc ou comportant des couleurs d’accentuation, les couleurs peuvent être renvoyées en codes de couleur hexadécimaux. Vision par ordinateur retourne également une valeur booléenne indiquant si une image est en noir et blanc.

## <a name="color-scheme-detection-examples"></a>Exemples de détection de jeux de couleurs

L’exemple suivant illustre la réponse JSON retournée par Vision par ordinateur lors de la détection du jeu de couleurs de l’image d’exemple. Dans ce cas, l’image d’exemple n’est pas une image en noir et blanc, mais les couleurs de premier plan et d’arrière-plan dominantes sont le noir, tandis que les couleurs dominantes de l’image dans son ensemble sont le noir et blanc.

![Montagne extérieur](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Exemples de couleur dominante

Le tableau suivant décrit les couleurs dominantes de premier plan, d’arrière-plan et d’image pour chaque exemple d’image, selon Vision par ordinateur.

| Image | Couleurs dominantes |
|-------|-----------------|
|![Fleur blanche sur arrière-plan de couleur blanche](./Images/flower.png)| Premier plan : Noir<br/>Arrière-plan : Blanc<br/>Couleurs : Noir, blanc, vert|
![Train traversant une gare](./Images/train_station.png) | Premier plan : Noir<br/>Arrière-plan : Noir<br/>Couleurs : Noir |

### <a name="accent-color-examples"></a>Exemples de couleur d’accentuation

 Le tableau suivant décrit la couleur d’accentuation, sous la forme d’une valeur de couleur HTML hexadécimale, pour chaque exemple d’image, selon Vision par ordinateur.

| Image | Couleur d’accentuation |
|-------|--------------|
|![Personne se tenant sur un rocher montagneux au coucher du soleil](./Images/mountain_vista.png) | #BB6D10 |
|![Fleur blanche sur arrière-plan de couleur blanche](./Images/flower.png) | #C6A205 |
|![Train traversant une gare](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exemples de détection pour le noir et blanc

Le tableau suivant indique si chaque exemple d’image est noir et blanc, selon Vision par ordinateur.

| Image | Noir et blanc ? |
|-------|----------------|
|![Image noir et blanc représentant des immeubles de Manhattan](./Images/bw_buildings.png) | true |
|![Maison bleu et jardin en façade ](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de [détection du type d’image](concept-detecting-image-types.md).