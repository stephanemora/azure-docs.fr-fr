---
title: 'L’API Vision par ordinateur avec cURL - Démarrage rapide : analyser une image locale | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous analysez une image locale à l’aide de l’API Vision par ordinateur avec cURL dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770381"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>Démarrage rapide : Analyser une image locale - REST, cURL

Dans ce démarrage rapide, vous analysez une image locale pour extraire des fonctionnalités visuelles à l’aide de l’API Vision par ordinateur. Pour analyser une image distante, consultez [Analyser une image distante avec cURL](curl-analyze.md).

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-local-image"></a>Analyser une image locale

Cet exemple est similaire à [Analyser une image distante avec cURL](curl-analyze.md), excepté que l’image à analyser est lue localement à partir du disque. Trois modifications sont nécessaires :

- Modifiez le type de contenu en `"Content-Type: application/octet-stream"`.
- Modifiez le commutateur `-d` en `--data-binary`.
- Spécifiez l’image à analyser à l’aide de la syntaxe suivante : `@C:/Pictures/ImageToAnalyze.jpg`.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez l’URL de requête (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
1. Remplacez `<Image To Analyze>` par l’image locale que vous souhaitez analyser.
1. Modifiez éventuellement la langue de la réponse (`language=en`).
1. Ouvrez une fenêtre de commandes sur un ordinateur sur lequel cURL est installé.
1. Collez le code dans la fenêtre et exécutez la commande.

>[!NOTE]
>Dans votre appel REST, vous devez utiliser le même emplacement que celui à partir duquel vous avez obtenu vos clés d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement à l’emplacement westus, remplacez « westcentralus » par « westus » dans l’URL ci-après.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
```

## <a name="analyze-image-response"></a>Réponse Analyser l’image

Une réponse correcte est retournée au format JSON, par exemple :

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Explorer les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit. Pour tester rapidement les API Vision par ordinateur, essayez la [console de test d’API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
