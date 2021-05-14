---
title: "Démarrage rapide : API REST Analyse d'images"
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour vous familiariser avec l'API REST Analyse d'images.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1d20e484b46dedfc5ecae0d24b4b30205cbe32cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800044"
---
Utilisez l'API REST Analyse d'images pour :

* Analyser une image pour identifier les étiquettes, la description textuelle, les visages, le contenu pour adultes, etc.
* Générer une image miniature avec rognage intelligent

> [!NOTE]
> Ce guide de démarrage rapide utilise des commandes cURL pour appeler l’API REST. Vous pouvez également appeler l’API REST à l’aide d’un langage de programmation. Vous pouvez consulter des exemples [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) et [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST) sur GitHub.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/) 
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* Installation de [cURL](https://curl.haxx.se/)

## <a name="analyze-an-image"></a>Analyser une image

Pour analyser les différentes caractéristiques visuelles d’une image, effectuez les étapes suivantes :

1. Copiez la commande ci-après dans un éditeur de texte.
1. Modifiez la commande comme ci-dessous :
    1. Remplacez la valeur de `<subscriptionKey>` par votre clé d’abonnement.
    1. Remplacez la première partie de l’URL de la demande (`westcentralus`) par le texte de votre propre URL de point de terminaison.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Éventuellement, changez l’URL d’image dans le corps de la requête (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) par l’URL d’une autre image à analyser.
1. Ouvrir une fenêtre d’invite de commandes.
1. Collez la commande à partir de l’éditeur de texte dans la fenêtre d’invite de commandes, puis exécutez la commande.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple d’application analyse et affiche une réponse réussie dans la fenêtre d’invite de commandes, comme dans l’exemple suivant :

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


## <a name="generate-a-thumbnail"></a>Générer une miniature

Vous pouvez utiliser Analyse d'images pour générer une miniature avec rognage intelligent. Spécifiez la hauteur et la largeur désirées, dont les proportions peuvent différer de l’image d’entrée. Analyse d'images utilise le rognage intelligent pour identifier la zone d'intérêt et générer des coordonnées de rognage autour de cette région.
 
Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez la commande ci-après dans un éditeur de texte.
1. Modifiez la commande comme ci-dessous :
    1. Remplacez la valeur de `<subscriptionKey>` par votre clé d’abonnement.
    1. Remplacez la valeur de `<thumbnailFile>` par le chemin et le nom du fichier dans lequel enregistrer la miniature retournée.
    1. Remplacez la première partie de l’URL de la demande (`westcentralus`) par le texte de votre propre URL de point de terminaison.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Éventuellement, changez l’URL d’image dans le corps de la requête (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) par l’URL d’une autre image à partir de laquelle générer une miniature.
1. Ouvrir une fenêtre d’invite de commandes.
1. Collez la commande à partir de l’éditeur de texte dans la fenêtre d’invite de commandes.
1. Appuyez sur Entrée pour exécuter le programme.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.2/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>Examiner la réponse

Une réponse réussie ajoute l’image miniature dans le fichier spécifié dans `<thumbnailFile>`. Si la requête échoue, la réponse contient un code d’erreur et un message pour vous aider à déterminer la cause du problème. Si la requête semble réussir, mais que la miniature créée n’est pas un fichier image valide, il est possible que votre clé d’abonnement ne soit pas valide.


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à effectuer des appels d’analyse d’image de base à l’aide de l’API REST. À présent, découvrez-en plus sur les fonctionnalités de l’API Analyser.

> [!div class="nextstepaction"]
>[Appeler l’API Analyser](../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Vue d’ensemble de l’analyse d’image](../overview-image-analysis.md)
