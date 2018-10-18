---
title: 'Démarrage rapide : Détecter les visages dans une image - API Visage, cURL'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez détecter les visages d’une image à l’aide de l’API Visage avec cURL.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: pafarley
ms.openlocfilehash: da18b7b3666863742f987b2ee0da297c0838d266
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343059"
---
# <a name="quickstart-detect-faces-in-an-image-using-curl"></a>Démarrage rapide : Détecter les visages dans une image à l’aide de cURL

Dans ce guide de démarrage rapide, vous allez détecter les visages d’une image à l’aide de l’API Visage.

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement pour exécuter l’exemple. Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Détecter des visages dans une image

Utilisez la méthode [Visage - Détecter](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour détecter les visages dans une image et retourner les attributs du visage, notamment :

* ID du visage : ID unique utilisé dans plusieurs scénarios d’API Visage.
* Rectangle du visage : valeurs gauche, haut, largeur et hauteur indiquant l’emplacement du visage dans l’image.
* Points de repère : tableau des 27 points de repère du visage pointant vers les positions importantes des composants du visage.
* Les attributs du visage, notamment l’âge, le sexe, l’intensité du sourire, la posture de la tête et la pilosité faciale.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Ouvrez une invite de commandes.
2. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
3. Remplacez l’URL (`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`) par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
4. Modifiez éventuellement l’image (`"{\"url\":...`) à analyser.
5. Collez le code dans la fenêtre Commande.
6. Exécutez la commande.

### <a name="face---detect-request"></a>Requête Visage - Détecter

> [!NOTE]
> Dans votre appel REST, vous devez utiliser le même emplacement que celui à partir duquel vous avez obtenu vos clés d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement à l’emplacement westus, remplacez « westcentralus » par « westus » dans l’URL ci-après.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>Réponse Visage - Détecter

Une réponse correcte est retournée au format JSON.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Visage utilisées pour détecter les visages humains dans une image, délimiter les visages avec des rectangles et retourner des attributs tels que l’âge et le sexe.

> [!div class="nextstepaction"]
> [API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
