---
title: 'Démarrage rapide : Reconnaître les émotions sur des visages dans une image - API Émotion, JavaScript'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Émotion avec JavaScript.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 96388bb3cf2b73000fe871bc9e08720038c5c5a3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234694"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Démarrage rapide : Générer une application permettant de reconnaître les émotions des visages dans une image.

> [!IMPORTANT]
> L’API Émotion sera déconseillée à partir du 15 février 2019. La fonction de reconnaissance des émotions est maintenant accessible de façon générale dans le cadre de l’[API Visage](https://docs.microsoft.com/azure/cognitive-services/face/). 

Cet article fournit des informations et des exemples de code qui vont vous aider à démarrer rapidement avec la [méthode Recognize de l’API Émotion](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) à l’aide de JavaScript afin de reconnaître les émotions exprimées par une ou plusieurs personnes dans une image.

## <a name="prerequisite"></a>Configuration requise
* Obtenez votre clé d’abonnement gratuite [ici](https://azure.microsoft.com/try/cognitive-services/) ou, si vous avez un abonnement Azure, créez une ressource API Émotion et obtenez votre clé d’abonnement et votre point de terminaison à partir de cette ressource.

![Créer la ressource API Émotion](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Exemple de requête de reconnaissance d’émotions avec JavaScript

Copiez le script suivant et enregistrez-le dans un fichier tel que `test.html`. Changez l’`url` de requête de façon à utiliser l’emplacement où vous avez obtenu vos clés d’abonnement, et remplacez la valeur « Ocp-Apim-Subscription-Key » par votre clé d’abonnement valide. Ces informations sont accessibles dans le portail Azure, respectivement dans les sections Vue d’ensemble et Clés de votre ressource API Émotion.

![Point de terminaison d’API](../Images/api-url.png)

![Clé d’abonnement d’API](../Images/keys.png)

Modifiez le corps de la requête de façon à utiliser l’emplacement d’une image que vous souhaitez utiliser. Pour exécuter l’exemple, glissez-déplacez le fichier dans votre navigateur.

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };

        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }

            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
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
