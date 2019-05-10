---
title: 'Démarrage rapide : Reconnaître l’encre numérique avec l’API REST de module de reconnaissance d’encre et Node.js'
description: Utilisez l’API de module de reconnaissance de l’encre pour démarrer la reconnaissance des traits d’encre numérique.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 651474fd538123e760022ac59efbbaf0b9b83d70
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519680"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Démarrage rapide : Reconnaître l’encre numérique avec l’API REST de module de reconnaissance d’encre et JavaScript

Utilisez ce guide de démarrage rapide pour commencer à utiliser l’API de module de reconnaissance de l’encre sur traits d’encre numérique. Cette application JavaScript envoie une demande d’API contenant des données de trait d’encre au format JSON et affiche la réponse.

Bien que cette application est écrite en Javascript et s’exécute dans votre navigateur web, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

En règle générale, vous appelez l’API à partir d’une application pour l’écriture manuscrite numérique. Ce démarrage rapide envoie des données de trait d’encre pour l’exemple suivant manuscrite à partir d’un fichier JSON.

![une image de texte manuscrit](../media/handwriting-sample.jpg)

Le code source de ce guide de démarrage rapide est disponible sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Conditions préalables

- Un navigateur web
- Vous trouverez l’exemple de données de trait d’encre pour ce démarrage rapide sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Dans votre IDE favori ou un éditeur, créez un nouveau `.html` fichier. Puis ajouter HTML de base pour le code que nous ajouterons plus tard.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Dans le `<body>` , ajoutez le code html suivant :
    1. Deux zones de texte pour l’affichage de la requête JSON et la réponse.
    2. Un bouton pour appeler le `recognizeInk()` (fonction) qui est créée ultérieurement.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Charger l’exemple de données JSON

1. Dans le `<script>` balise, créez une variable pour le sampleJson. Puis créez une fonction JavaScript nommée `openFile()` qui ouvre un Explorateur de fichiers pour vous pouvez de sélectionner votre fichier JSON. Lorsque le `Recognize ink` bouton est activé, il appelle cette fonction et commencer la lecture du fichier.
2. Utilisez un `FileReader` l’objet `onload()` fonction pour traiter le fichier de façon asynchrone. 
    1. Remplacer les `\n` ou `\r` caractères dans le fichier avec une chaîne vide. 
    2. Utilisez `JSON.parse()` pour convertir le texte JSON valid
    3. Mise à jour le `request` zone de texte dans l’application. Utilisez `JSON.stringify()` à mettre en forme la chaîne JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Envoyer une demande à l’API de module de reconnaissance de l’encre

1. Dans le `<script>` balise, créez une fonction appelée `recognizeInk()`. Cette fonction sera ultérieurement appeler l’API et mettre à jour la page avec la réponse. Ajoutez le code dans les étapes suivantes dans cette fonction. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Créer des variables pour votre URL de point de terminaison, clé d’abonnement et l’exemple JSON. Créez ensuite un `XMLHttpRequest` objet pour envoyer la demande d’API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Créer la fonction de retour pour la `XMLHttpRequest` objet. Cette fonction sera analyser la réponse d’API à partir d’une demande réussie et affichez-la dans l’application. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Créer la fonction d’erreur pour l’objet de requête. Cette fonction enregistre l’erreur dans la console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Créer une fonction de l’objet de demande `onreadystatechange` propriété. Lors de l’état de préparation de l’objet de demande change, les fonctions de retour et d’erreur ci-dessus s’appliqueront.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Envoyer la demande d’API. Ajoutez votre clé d’abonnement pour le `Ocp-Apim-Subscription-Key` en-tête, puis définissez le `content-type` à `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
