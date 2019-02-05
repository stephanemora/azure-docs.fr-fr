---
title: 'Démarrage rapide : Reconnaître les émotions sur des visages dans une image - API Émotion, cURL'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Émotion avec cURL.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 2cd139fc47177d429bada454a5e720b7eb4f192b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220550"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Démarrage rapide : Générer une application permettant de reconnaître les émotions des visages dans une image.

> [!IMPORTANT]
> L’API Émotion sera déconseillée à partir du 15 février 2019. La fonction de reconnaissance des émotions est maintenant accessible de façon générale dans le cadre de l’[API Visage](https://docs.microsoft.com/azure/cognitive-services/face/).

Cet article fournit des informations et des exemples de code pour une prise en main rapide de la [méthode Recognize de l’API Émotion](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) avec cURL afin de reconnaître les émotions exprimées par une ou plusieurs personnes dans une image.

## <a name="prerequisite"></a>Configuration requise
* Obtenez votre clé d’abonnement gratuite [à cette adresse](https://azure.microsoft.com/try/cognitive-services/)

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
Un appel réussi retourne un tableau comprenant les entrées de visages et les résultats d’émotion associés, classés par taille du rectangle du visage dans l’ordre décroissant. Une réponse vide indique qu’aucun visage n’a été détecté. Une entrée d’émotion contient les champs suivants :
* faceRectangle : emplacement du rectangle du visage sur l’image.
* scores : résultats d’émotion pour chaque visage sur l’image.

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
