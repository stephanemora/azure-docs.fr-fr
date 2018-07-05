---
title: Démarrage rapide de l’API Émotion avec Ruby | Microsoft Docs
description: Obtenez des informations et des exemples de code pour une prise en main rapide à l’aide de l’API Émotion avec Ruby dans Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 733127bb3656d86a7f3f57cd26c72909900f4899
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021047"
---
# <a name="emotion-api-ruby-quick-start"></a>Démarrage rapide de l’API Émotion avec Ruby

> [!IMPORTANT]
> La préversion de l’API Video prendra fin le 30 octobre 2017. Essayez la préversion de la nouvelle [API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) pour extraire facilement des insights des vidéos et améliorer les expériences de découverte de contenu telles que les résultats de recherche en détectant les mots prononcés, les visages, les personnes et les émotions. [Plus d’informations](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)

Cet article fournit des informations et des exemples de code pour une prise en main rapide à l’aide de la [méthode Recognize de l’API Emotion](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) avec Ruby pour reconnaître les émotions exprimées par une ou plusieurs personnes dans une image.

## <a name="prerequisite"></a>Configuration requise
* Obtenez votre clé d’abonnement gratuite [à cette adresse](https://azure.microsoft.com/try/cognitive-services/).

## <a name="recognize-emotions-ruby-example-request"></a>Exemple de requête de reconnaissance d’émotions de Ruby

Changez l’URL REST et utilisez l’emplacement où vous avez obtenu vos clés d’abonnement. Remplacez la valeur « Ocp-Apim-Subscription-Key » par votre clé d’abonnement valide, et ajoutez une URL vers une photo à la variable `body`.

```ruby
require 'net/http'

# NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
#   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
#   URL below with "westcentralus".
uri = URI('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize')
uri.query = URI.encode_www_form({
})

request = Net::HTTP::Post.new(uri.request_uri)
# Request headers
request['Content-Type'] = 'application/json'
# NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '13hc77781f7e4b19b5fcdd72a8df7156'
# Request body
request.body = "{\"url\":\"http://example.com/1.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body

```

## <a name="recognize-emotions-sample-response"></a>Exemples de réponse de la reconnaissance d’émotions
Un appel réussi retourne un tableau comprenant les entrées de visages et les résultats d’émotion associés, classés par taille du rectangle du visage par ordre décroissant. Une réponse vide indique qu’aucun visage n’a été détecté. Une entrée de visage contient les champs suivants :
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
