---
title: 'Démarrage rapide : Reconnaissance des émotions sur les visages dans une image - API Émotion, Ruby'
titlesuffix: Azure Cognitive Services
description: Obtenez des informations et des exemples de code pour une prise en main rapide de l’API Émotion avec Ruby.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: bcab24334c1ee4e47061ce6ea28bd60039e17b3f
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239027"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Démarrage rapide : Création d’une application permettant de reconnaître les émotions sur les visages dans une image.

> [!IMPORTANT]
> L’API Émotion sera déconseillée à partir du 15 février 2019. La fonction de reconnaissance des émotions est maintenant accessible de façon générale dans le cadre de l’[API Visage](https://docs.microsoft.com/azure/cognitive-services/face/). 

Cet article fournit des informations et des exemples de code pour une prise en main rapide à l’aide de la [méthode Recognize de l’API Emotion](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) avec Ruby pour reconnaître les émotions exprimées par une ou plusieurs personnes dans une image.

## <a name="prerequisite"></a>Configuration requise
* Obtenez votre clé d’abonnement gratuite [à cette adresse](https://azure.microsoft.com/try/cognitive-services/)

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
