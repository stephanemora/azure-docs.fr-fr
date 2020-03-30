---
title: 'Démarrage rapide : Détecter des visages dans une image avec l’API REST Azure et cURL'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec cURL pour détecter des visages dans une image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 6a1a6d1fdce4853a2ac73f10eb4cf0a0505fa4c7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165906"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Démarrage rapide : Détecter des visages dans une image à l’aide de l’API REST Visage et de cURL

Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec cURL pour détecter des visages humains dans une image.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Conditions préalables requises

- Clé d’abonnement au service Visage. Vous pouvez obtenir une clé d’abonnement d’essai gratuit à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Vous pouvez également suivre les instructions fournies dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service Visage et obtenir votre clé.

## <a name="write-the-command"></a>Écrire la commande
 
Vous allez utiliser une commande similaire à ce qui suit pour appeler l’API Visage et obtenir les données d’attribut de visage d’une image. Pour commencer, copiez le code dans un éditeur de texte. Avant de l’exécuter, vous devez changer certaines parties.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Clé d’abonnement
Remplacez `<Subscription Key>` par votre clé d’abonnement Visage valide.

### <a name="face-endpoint-url"></a>URL du point de terminaison Visage

L’URL `https://<My Endpoint String>.com/face/v1.0/detect` indique le point de terminaison Visage Azure à interroger. Vous devrez peut-être changer la première partie de cette URL pour qu’elle corresponde au point de terminaison associé à votre clé d’abonnement.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>Chaîne de requête d’URL

La chaîne de requête de l’URL du point de terminaison Visage spécifie les attributs de visage à récupérer. Vous pouvez changer cette chaîne en fonction de votre utilisation prévue.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL source de l’image
L’URL source indique l’image à utiliser comme entrée. Vous pouvez la faire pointer vers n’importe quelle image à analyser.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Exécutez la commande.

Après avoir apporté vos changements, ouvrez une invite de commandes et entrez la nouvelle commande. Les informations de visage doivent apparaître en tant que données JSON dans la fenêtre de console. Par exemple :

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

Dans ce guide de démarrage rapide, vous avez écrit une commande cURL qui appelle le service Visage Azure pour détecter des visages dans une image et retourner leurs attributs. Explorez à présent la documentation de référence sur l’API Visage pour en savoir plus.

> [!div class="nextstepaction"]
> [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
