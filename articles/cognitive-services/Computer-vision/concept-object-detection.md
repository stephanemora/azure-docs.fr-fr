---
title: Détection d’objets - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la détection d’objets à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 89323e584b4020613fe3ff8411df50f2ab96f156
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582726"
---
# <a name="object-detection"></a>Détection d’objets

La détection d’objets est similaire au [balisage](concept-tagging-images.md), mais l’API retourne les coordonnées de cadre englobant (en pixels) pour chaque objet trouvé. Par exemple, si une image contient un chien, un chat et une personne, l’opération de détection liste ces objets ainsi que leurs coordonnées dans l’image. Vous pouvez utiliser cette fonctionnalité pour traiter les relations entre les objets dans une image. Elle vous permet également de déterminer s’il existe plusieurs instances de la même balise dans une image.

L’API Détection applique des balises en fonction des objets ou éléments vivants identifiés dans l’image. Notez qu’à ce stade il n’existe aucune relation formelle entre la taxonomie utilisée pour le balisage et la taxonomie utilisée pour la détection d’objets. À un niveau conceptuel, l’API Détection recherche uniquement les objets et les éléments vivants, tandis que l’API Balise peut également inclure des termes contextuels tels que « intérieur », qui ne peuvent pas être localisés avec des cadres englobants.

## <a name="object-detection-example"></a>Exemple de détection d’objet

La réponse JSON suivante illustre ce que retourne Vision par ordinateur lors de la détection d’objets dans l’exemple d’image.

![Une femme utilisant un appareil Microsoft Surface dans une cuisine](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de [classement d’images](concept-categorizing-images.md) et de [description des images](concept-describing-images.md).