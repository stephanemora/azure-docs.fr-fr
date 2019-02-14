---
title: 'Démarrage rapide : Obtenir des insights sur les images à l’aide de l’API REST Recherche visuelle Bing et de Node.js'
titleSuffix: Azure Cognitive Services
description: Découvrez comment charger une image dans l’API Recherche visuelle Bing pour récupérer des insights sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 7a0103e21b4c287526e53b9f886e98027f49c392
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863990"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Démarrage rapide : Obtenir des insights sur les images à l’aide de l’API REST Recherche visuelle Bing et de Node.js

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche visuelle Bing et voir les résultats de la recherche. Cette application JavaScript simple charge une image dans l’API et affiche les informations retournées à son sujet. Alors que cette application est écrite en JavaScript, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Quand vous chargez une image locale, les données du formulaire doivent inclure l’en-tête Content-Disposition. Son paramètre `name` doit être défini sur « image » tandis que le paramètre `filename` peut être défini sur une chaîne quelle qu’elle soit. Le contenu du formulaire correspond au code binaire de l’image. La taille maximale de l’image que vous chargez est de 1 Mo.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/en/download/)
* Module Request pour JavaScript
    * Vous pouvez installer ce module à l’aide de la commande `npm install request`.
* Module form-data
    * Vous pouvez installer ce module à l’aide de la commande `npm install form-data`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>Initialiser l’application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis définissez les exigences suivantes :

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Créez des variables pour le point de terminaison de l’API, la clé d’abonnement et le chemin de votre image.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Créez une fonction appelée `requestCallback()` pour afficher la réponse de l’API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construire et envoyer la demande de recherche

1. Créez des données de formulaire à l’aide de `FormData()` et ajoutez-y le chemin d’accès de l’image, à l’aide de `fs.createReadStream()`.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Utilisez la bibliothèque de demande pour charger l’image, en appelant `requestCallback()` pour afficher la réponse. Veillez à ajouter votre clé d’abonnement à l’en-tête de la demande. 

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Recherche personnalisée](../tutorial-bing-visual-search-single-page-app.md)
