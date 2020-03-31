---
title: 'Démarrage rapide : Vision par ordinateur 2.0 et 2.1 - Extraire du texte imprimé et manuscrit - REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez extraire le texte imprimé et manuscrit d’une image en utilisant l’API Vision par ordinateur avec JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8c3f5dae62aef6c8e8ec1eeaeb712ebff67397c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566180"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-20-and-21-rest-api-and-javascript"></a>Démarrage rapide : Extraire du texte imprimé et manuscrit à l’aide de l’API REST Vision par ordinateur 2.0 ou 2.1 et de JavaScript

Dans ce guide de démarrage rapide, vous allez extraire le texte imprimé et/ou manuscrit d’une image en utilisant l’API REST de Vision par ordinateur. Avec les méthodes [Batch Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) et [Read Operation Result](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d), vous pouvez détecter le texte dans une image et extraire les caractères reconnus dans un flux de caractères exploitable automatiquement. L’API détermine le modèle de reconnaissance à utiliser pour chaque ligne de texte. Elle prend donc en charge les images contenant à la fois du texte imprimé et manuscrit.

Par rapport à Vision par ordinateur 2.0 et 2.1, la préversion publique Vision par ordinateur 3.0 offre :

* Une justesse encore supérieure
* Un nouveau format de sortie
* Un score de confiance pour les mots
* La prise en charge de l’espagnol et de l’anglais avec le paramètre de langue supplémentaire

#### <a name="version-2"></a>[Version 2](#tab/version-2)

> [!IMPORTANT]
> La méthode [Batch Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) s’exécute de façon asynchrone. Cette méthode ne retourne pas d’informations dans le corps d’une réponse réussie. Au lieu de cela, la méthode Batch Read retourne un URI dans la valeur du champ d’en-tête de réponse `Operation-Location`. Vous pouvez ensuite appeler cet URI, qui représente l’API [Read Operation Result](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d), pour vérifier l’état de l’appel de la méthode Batch Read et retourner les résultats.

#### <a name="version-3-public-preview"></a>[Version 3 (préversion publique)](#tab/version-3)

> [!IMPORTANT]
> La méthode [Batch Read](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005) s’exécute de façon asynchrone. Cette méthode ne retourne pas d’informations dans le corps d’une réponse réussie. Au lieu de cela, la méthode Batch Read retourne un URI dans la valeur du champ d’en-tête de réponse `Operation-Location`. Vous pouvez ensuite appeler cet URI, qui représente l’API [Read Operation Result](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750), pour vérifier l’état de l’appel de la méthode Batch Read et retourner les résultats.

---

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

Vous devez disposer d’une clé d’abonnement pour la Vision par ordinateur. Vous pouvez obtenir une clé d’essai gratuit auprès de [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vous pouvez également suivre les instructions mentionnées dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Vision par ordinateur et obtenir votre clé. Ensuite, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la chaîne de point de terminaison de la clé et du service, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Création et exécution de l’exemple

#### <a name="version-2"></a>[Version 2](#tab/version-2)

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur de texte.
1. Remplacez éventuellement la valeur de l’attribut `value` du contrôle `inputImage` par l’URL d’une autre image à partir de laquelle vous voulez extraire le texte.
1. Enregistrez le code dans un fichier avec une extension `.html`. Par exemple : `get-text.html`.
1. Ouvrez une fenêtre de navigateur.
1. Dans le navigateur, faites glisser et déposez le fichier dans la fenêtre du navigateur.
1. Quand la page web s’affiche dans le navigateur, choisissez le bouton **Lire l’image**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
        let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
        if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }
        
        var uriBase = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

#### <a name="version-3-public-preview"></a>[Version 3 (préversion publique)](#tab/version-3)

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur de texte.
1. Remplacez éventuellement la valeur de l’attribut `value` du contrôle `inputImage` par l’URL d’une autre image à partir de laquelle vous voulez extraire le texte.
1. Enregistrez le code dans un fichier avec une extension `.html`. Par exemple : `get-text.html`.
1. Ouvrez une fenêtre de navigateur.
1. Quand la page web s’affiche dans le navigateur, renseignez les paramètres requis et choisissez le bouton **Lire l’image**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = document.getElementById("key").value;
        let endpoint = document.getElementById("endpoint").value;
        if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }
        let language = document.getElementById("language").value;
        
        var uriBase = endpoint + "/vision/v3.0-preview/read/analyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        const params = {
            language: language,
        };

        const searchParams = new URLSearchParams(params);

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase + "?" + searchParams.toString(),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: JSON.stringify({url: sourceImageUrl}),
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Endpoint: 
<input type="text" name="endpoint" id="endpoint" value="" style="width: 300px;"/>
<div style="margin: 20px;">Example: https://westus2.api.cognitive.microsoft.com</div>
Subscription Key:    
<input type="text" name="key" id="key" value="" style="width: 300px;"/>
<br><br>
Language: 
<input type="text" name="language" id="language"
    value="en" />
<div style="margin: 20px;">Accepted values are &quot;en&quot; and &quot;es&quot;</div>

<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

---

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple de page web analyse et affiche une réponse réussie dans la fenêtre du navigateur, comme dans l’exemple suivant :

#### <a name="version-2"></a>[Version 2](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="version-3-public-preview"></a>[Version 3 (préversion publique)](#tab/version-3)

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-02-11T20:56:33Z",
  "lastUpdatedDateTime": "2020-02-11T20:56:34Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8206,
        "width": 800,
        "height": 154,
        "unit": "pixel",
        "lines": [
          {
            "language": "en",
            "boundingBox": [
              6,
              4,
              774,
              14,
              773,
              61,
              5,
              49
            ],
            "text": "The quick brown fox jumps over the lazy",
            "words": [
              {
                "boundingBox": [
                  14,
                  5,
                  76,
                  6,
                  74,
                  49,
                  12,
                  48
                ],
                "text": "The",
                "confidence": 0.83
              },
              {
                "boundingBox": [
                  84,
                  6,
                  182,
                  7,
                  180,
                  51,
                  82,
                  49
                ],
                "text": "quick",
                "confidence": 0.762
              },
              {
                "boundingBox": [
                  191,
                  7,
                  312,
                  9,
                  309,
                  54,
                  189,
                  51
                ],
                "text": "brown",
                "confidence": 0.67
              },
              {
                "boundingBox": [
                  320,
                  9,
                  382,
                  10,
                  379,
                  55,
                  317,
                  54
                ],
                "text": "fox",
                "confidence": 0.849
              },
              {
                "boundingBox": [
                  390,
                  10,
                  497,
                  11,
                  493,
                  57,
                  387,
                  55
                ],
                "text": "jumps",
                "confidence": 0.703
              },
              {
                "boundingBox": [
                  506,
                  11,
                  596,
                  12,
                  591,
                  59,
                  502,
                  57
                ],
                "text": "over",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  604,
                  12,
                  666,
                  13,
                  661,
                  60,
                  600,
                  59
                ],
                "text": "the",
                "confidence": 0.923
              },
              {
                "boundingBox": [
                  674,
                  13,
                  773,
                  14,
                  768,
                  62,
                  670,
                  60
                ],
                "text": "lazy",
                "confidence": 0.863
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              5,
              53,
              79,
              56,
              77,
              95,
              4,
              92
            ],
            "text": "dog",
            "words": [
              {
                "boundingBox": [
                  6,
                  53,
                  74,
                  56,
                  72,
                  95,
                  5,
                  92
                ],
                "text": "dog",
                "confidence": 0.418
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              0,
              90,
              787,
              95,
              787,
              145,
              0,
              136
            ],
            "text": "Pack my box with five dozen liquor jugs",
            "words": [
              {
                "boundingBox": [
                  1,
                  96,
                  79,
                  93,
                  79,
                  135,
                  0,
                  136
                ],
                "text": "Pack",
                "confidence": 0.835
              },
              {
                "boundingBox": [
                  87,
                  93,
                  151,
                  92,
                  151,
                  135,
                  87,
                  135
                ],
                "text": "my",
                "confidence": 0.88
              },
              {
                "boundingBox": [
                  162,
                  92,
                  226,
                  91,
                  225,
                  135,
                  161,
                  135
                ],
                "text": "box",
                "confidence": 0.301
              },
              {
                "boundingBox": [
                  234,
                  91,
                  335,
                  90,
                  335,
                  135,
                  233,
                  135
                ],
                "text": "with",
                "confidence": 0.959
              },
              {
                "boundingBox": [
                  346,
                  91,
                  418,
                  91,
                  417,
                  136,
                  345,
                  135
                ],
                "text": "five",
                "confidence": 0.489
              },
              {
                "boundingBox": [
                  426,
                  91,
                  527,
                  93,
                  527,
                  138,
                  425,
                  136
                ],
                "text": "dozen",
                "confidence": 0.727
              },
              {
                "boundingBox": [
                  554,
                  94,
                  687,
                  98,
                  687,
                  143,
                  553,
                  139
                ],
                "text": "liquor",
                "confidence": 0.377
              },
              {
                "boundingBox": [
                  701,
                  99,
                  787,
                  103,
                  787,
                  146,
                  700,
                  143
                ],
                "text": "jugs",
                "confidence": 0.693
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

## <a name="next-steps"></a>Étapes suivantes

Explorez une application JavaScript qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutoriel sur l’API Vision par ordinateur JavaScript](../Tutorials/javascript-tutorial.md)
