---
title: 'Démarrage rapide : Créer une requête de recherche visuelle, Node.js - Recherche visuelle Bing'
titleSuffix: Azure Cognitive Services
description: Montre comment charger une image dans l’API Recherche visuelle Bing pour récupérer des informations sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3490f7722ca0c1331ccea26cd18398cff1317aee
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887410"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-javascript"></a>Démarrage rapide : Votre première requête Recherche visuelle Bing en JavaScript

L’API Recherche visuelle Bing renvoie des informations sur une image que vous fournissez. Cette image est accessible via son URL, un jeton insights ou par chargement. Pour plus d’informations sur ces options, consultez la section [Qu’est-ce que l’API Recherche visuelle Bing ?](../overview.md) Cet article explique comment charger une image. Il peut se révéler utile de charger une image prise sur un appareil mobile. Par exemple, vous photographiez un monument très connu pour obtenir en retour des informations sur celui-ci. Ces informations sont parfois très variées. 

Si vous chargez une image enregistrée en local, voici les données de formulaire que vous devez inclure dans le corps du POST. Les données du formulaire doivent inclure l’en-tête Content-Disposition. Son paramètre `name` doit être défini sur « image » tandis que le paramètre `filename` peut être défini sur une chaîne quelle qu’elle soit. Le contenu du formulaire correspond au code binaire de l’image. La taille maximale de l’image que vous chargez est de 1 Mo. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Cet article inclut une application console qui envoie une requête d’API Recherche visuelle Bing et affiche les résultats de la recherche au format JSON. Alors que cette application est écrite en JavaScript, l’API est un service web RESTful compatible avec n’importe quel langage de programmation qui peut formuler des requêtes HTTP et analyser JSON. 

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Node.js 6](https://nodejs.org/en/download/) pour exécuter ce code.

Pour ce démarrage rapide, vous pouvez utiliser une clé d’abonnement en [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou en version payante.

## <a name="running-the-application"></a>Exécution de l'application

La procédure suivante montre comment envoyer un SMS à l’aide de FormData dans Node.js.

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Créez un dossier pour votre projet (ou utilisez votre éditeur ou IDE préféré).
2. À partir d’un terminal ou d’une invite de commandes, accédez au dossier que vous venez de créer.
3. Installez les modules request :  
  ```  
  npm install request  
  ```  
3. Installez les modules form-data :  
  ```  
  npm install form-data  
  ```  
4. Créez un fichier nommé GetVisualInsights.js et ajoutez le code suivant à ce dernier.
5. Remplacez la valeur `subscriptionKey` par votre clé d’abonnement.
6. Remplacez la valeur `imagePath` par le chemin d’accès de l’image à charger.
7. Exécutez le programme.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>Étapes suivantes

[Obtenir des informations sur une image à l’aide d’un jeton insights](../use-insights-token.md)  
[Tutoriel de chargement d’images pour Recherche visuelle Bing](../tutorial-visual-search-image-upload.md)
[Tutoriel de l’application à page unique Recherche visuelle Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Présentation de l’API Recherche visuelle Bing](../overview.md)  
[Essayer](https://aka.ms/bingvisualsearchtryforfree)  
[Obtenir une clé d’accès d’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informations de référence sur l’API Recherche visuelle Bing](https://aka.ms/bingvisualsearchreferencedoc)
