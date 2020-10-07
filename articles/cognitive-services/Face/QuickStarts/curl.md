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
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 8afb6f018e9c01ee42a9e43cc726a442fa4c8965
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88539336"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Démarrage rapide : Détecter des visages dans une image à l’aide de l’API REST Visage et de cURL

Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec cURL pour détecter des visages humains dans une image.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="créez une ressource Visage"  target="_blank">créer une ressource Visage <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le Portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Visage. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

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
