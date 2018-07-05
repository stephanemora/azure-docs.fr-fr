---
title: Démarrage rapide de l’API Émotion avec cURL | Microsoft Docs
description: Obtenez des informations et des exemples de code pour une prise en main rapide de l’API Émotion avec cURL dans Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: a7ca2cac718797462bb4dc889b3f1361b252435e
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021096"
---
# <a name="emotion-api-curl-quick-start"></a>Démarrage rapide de l’API Émotion avec cURL

> [!IMPORTANT]
> La préversion de l’API Vidéo a pris fin le 30 octobre 2017. Essayez la nouvelle [préversion de l’API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) pour extraire facilement des insights des vidéos et améliorer les expériences de découverte de contenu, telles que les résultats de recherche, en détectant le texte parlé, les visages, les personnes et les émotions. [Plus d’informations](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)

Cet article fournit des informations et des exemples de code pour une prise en main rapide de la [méthode Recognize de l’API Émotion](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) avec cURL afin de reconnaître les émotions exprimées par une ou plusieurs personnes dans une image. 

## <a name="prerequisite"></a>Configuration requise
* Obtenez votre clé d’abonnement gratuite [à cette adresse](https://azure.microsoft.com/try/cognitive-services/).

## <a name="recognize-emotions-curl-example-request"></a>Exemple de requête de reconnaissance d’émotions avec cURL

> [!NOTE]
> Dans votre appel REST, vous devez utiliser le même emplacement que celui à partir duquel vous avez obtenu vos clés d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement à l’emplacement westcentralus, remplacez « westus » par « westcentralus » dans l’URL ci-après.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}" 
```

## <a name="recognize-emotions-sample-response"></a>Exemple de réponse de la reconnaissance d’émotions
Un appel réussi renvoie un tableau comprenant les entrées de visages et les résultats d’émotion associés, classés par taille du rectangle du visage dans l’ordre décroissant. Une réponse vide indique qu’aucun visage n’a été détecté. Une entrée d’émotion contient les champs suivants :
* faceRectangle : emplacement du rectangle du visage sur l’image.
* scores : résultats d’émotion pour chaque visage sur l’image. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

