---
title: Contenu spécifique à un domaine - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Apprenez à spécifier un domaine de catégorisation d'image pour renvoyer des informations plus détaillées sur une image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68946337"
---
# <a name="detect-domain-specific-content"></a>Détecter le contenu spécifique à un domaine

Outre le balisage et la catégorisation de haut niveau, le service Vision par ordinateur prend également en charge d'autres analyses spécifiques à un domaine en utilisant des modèles formés à partir de données spécialisées.

Les modèles spécifiques à un domaine peuvent être utilisés de deux façons : en l'état (analyse élargie) ou en tant qu'amélioration de la fonctionnalité de catégorisation.

### <a name="scoped-analysis"></a>Analyse élargie

Vous pouvez analyser une image à l’aide du modèle spécifique au domaine choisi en appelant l’API [Models/\<model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200).

Voici un exemple de réponse JSON renvoyé par l'API **models/celebrities/analyze** pour l'image donnée :

![Satya Nadella debout, souriant](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Analyse de catégorisation améliorée

Vous pouvez également utiliser des modèles spécifiques à un domaine pour compléter l'analyse d'image générale. Pour ce faire, dans le cadre de la [catégorisation de haut niveau](concept-categorizing-images.md), vous devez spécifier des modèles spécifiques à un domaine dans le paramètre *details* de l'appel d'API [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

Dans ce cas, le classifieur de la taxonomie des 86 catégories est appelé en premier. Si l'une des catégories détectées possède un modèle spécifique au domaine correspondant, l'image est également transmise à travers ce modèle et les résultats sont ajoutés.

La réponse JSON suivante montre comment une analyse spécifique à un domaine peut être incluse en tant que nœud `detail` dans une analyse de catégorisation plus large.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Dresser la liste des modèles spécifiques à un domaine

Le service Vision par ordinateur prend actuellement en charge les modèles spécifiques à un domaine suivants :

| Name | Description |
|------|-------------|
| celebrities | Reconnaissance des célébrités, prise en charge pour les images classées dans la catégorie `people_` |
| landmarks | Reconnaissance des points de repère, prise en charge pour les images classées dans les catégories `outdoor_` ou `building_` |

L'appel de l'API [Models](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) renvoie ces informations, ainsi que les catégories auxquelles chaque modèle peut s'appliquer :

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts liés à la [catégorisation des images](concept-categorizing-images.md).
