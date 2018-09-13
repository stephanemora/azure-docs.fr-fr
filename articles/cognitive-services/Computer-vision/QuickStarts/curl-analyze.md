---
title: 'L’API Vision par ordinateur avec cURL - Démarrage rapide : analyser une image distante | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous analysez une image distante à l’aide de l’API Vision par ordinateur avec cURL dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: c092432adbcba9cf7a131584131fd05991d1edef
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770302"
---
# <a name="quickstart-analyze-a-remote-image---rest-curl"></a>Démarrage rapide : Analyser une image distante - REST, cURL

Dans ce démarrage rapide, vous analysez une image distante pour extraire des fonctionnalités visuelles à l’aide de l’API Vision par ordinateur. Pour analyser une image locale, consultez [Analyser une image locale avec cURL](curl-disk.md).

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-remote-image"></a>Analyser une image distante

Avec la [méthode d’analyse d’une image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), vous pouvez extraire des fonctionnalités visuelles en fonction du contenu de l’image. Vous pouvez télécharger une image ou spécifier l’URL d’une image et choisir les fonctionnalités à retourner, notamment :

* Une liste détaillée des balises liées au contenu de l’image.
* Une description du contenu de l’image dans une phrase complète.
* Les coordonnées, le sexe et l’âge de n’importe quel visage contenu dans l’image.
* Le type d’image (images clipart ou dessin au trait).
* La couleur dominante, la couleur d’accentuation, et si une image est en noir et blanc.
* La catégorie définie dans cette [taxonomie](../Category-Taxonomy.md).
* L’image contient-elle un contenu pour adultes ou sexuellement suggestif ?

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez l’URL de requête (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
1. Modifiez éventuellement l’image (`{\"url\":\"...`) à analyser.
1. Modifiez éventuellement la langue de la réponse (`language=en`).
1. Ouvrez une fenêtre de commandes sur un ordinateur sur lequel cURL est installé.
1. Collez le code dans la fenêtre et exécutez la commande.

>[!NOTE]
>Dans votre appel REST, vous devez utiliser le même emplacement que celui à partir duquel vous avez obtenu vos clés d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement à l’emplacement westus, remplacez « westcentralus » par « westus » dans l’URL ci-après.

## <a name="analyze-image-request"></a>Requête Analyser l’image

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
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
