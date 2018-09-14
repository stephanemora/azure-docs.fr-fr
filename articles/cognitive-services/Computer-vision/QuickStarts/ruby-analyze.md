---
title: Démarrage rapide API Vision par ordinateur avec Ruby pour analyser une image | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous allez analyser une image à l’aide de l’API Vision par ordinateur avec Ruby dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 3ce89bf29cc7f1f436e54d398e458f559b79a425
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770141"
---
# <a name="quickstart-analyze-a-remote-image---rest-ruby"></a>Démarrage rapide : Analyser une image distante - REST, Ruby

Dans ce démarrage rapide, vous allez analyser une image pour extraire des fonctionnalités visuelles à l’aide de l’API Vision par ordinateur.

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [How to obtain subscription keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Obtention de clés d’abonnement).

## <a name="analyze-image-request"></a>Requête Analyser l’image

Avec la [méthode Analyser l’image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), vous pouvez extraire des fonctionnalités visuelles en fonction du contenu de l’image. Vous pouvez charger une image ou spécifier l’URL d’une image et choisir les fonctionnalités à renvoyer, notamment :

* Une liste détaillée des balises liées au contenu de l’image.
* Une description du contenu de l’image dans une phrase complète.
* Les coordonnées, le sexe et l’âge de n’importe quel visage contenu dans l’image.
* L’élément ImageType, qui correspond au type d’image (images clipart ou dessin au trait).
* La couleur dominante, la couleur d’accentuation, et si une image est en noir et blanc.
* La catégorie définie dans cette [taxonomie](../Category-Taxonomy.md).
* L’image contient-elle un contenu pour adultes ou sexuellement suggestif ?

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez la valeur `uri` par l’emplacement où vous avez obtenu vos clés d’abonnement, le cas échéant.
1. Modifiez éventuellement la langue de la réponse (`'language' => 'en'`).
1. Modifiez éventuellement l’image (`{\"url\":\"...`) à analyser.
1. Enregistrez le fichier avec une extension `.rb`.
1. Ouvrez l’invite de commandes Ruby et exécutez le fichier, par exemple : `ruby myfile.rb`.

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze')
uri.query = URI.encode_www_form({
    # Request parameters
    'visualFeatures' => 'Categories, Description',
    'details' => 'Landmarks',
    'language' => 'en'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

## <a name="analyze-image-response"></a>Réponse Analyser l’image

Une réponse correcte est retournée au format JSON, par exemple :

```json
{
  "categories": [
    {
      "name": "abstract_",
      "score": 0.00390625
    },
    {
      "name": "people_",
      "score": 0.83984375,
      "detail": {
        "celebrities": [
          {
            "name": "Satya Nadella",
            "faceRectangle": {
              "left": 597,
              "top": 162,
              "width": 248,
              "height": 248
            },
            "confidence": 0.999028444
          }
        ]
      }
    }
  ],
  "adult": {
    "isAdultContent": false,
    "isRacyContent": false,
    "adultScore": 0.0934349000453949,
    "racyScore": 0.068613491952419281
  },
  "tags": [
    {
      "name": "person",
      "confidence": 0.98979085683822632
    },
    {
      "name": "man",
      "confidence": 0.94493889808654785
    },
    {
      "name": "outdoor",
      "confidence": 0.938492476940155
    },
    {
      "name": "window",
      "confidence": 0.89513939619064331
    }
  ],
  "description": {
    "tags": [
      "person",
      "man",
      "outdoor",
      "window",
      "glasses"
    ],
    "captions": [
      {
        "text": "Satya Nadella sitting on a bench",
        "confidence": 0.48293603002174407
      }
    ]
  },
  "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
  "metadata": {
    "width": 1500,
    "height": 1000,
    "format": "Jpeg"
  },
  "faces": [
    {
      "age": 44,
      "gender": "Male",
      "faceRectangle": {
        "left": 593,
        "top": 160,
        "width": 250,
        "height": 250
      }
    }
  ],
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown",
      "Black"
    ],
    "accentColor": "873B59",
    "isBWImg": false
  },
  "imageType": {
    "clipArtType": 0,
    "lineDrawingType": 0
  }
}

```

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit. Pour tester rapidement les API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
