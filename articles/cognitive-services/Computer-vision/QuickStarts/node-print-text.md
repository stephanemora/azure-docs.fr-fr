---
title: 'Démarrage rapide : Extraire le texte imprimé - REST, Node.js'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous extrayez le texte imprimé d’une image en utilisant l’API Vision par ordinateur avec Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 55aeba04941f4baf233752fc84b8880529b1911d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404640"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-nodejs"></a>Démarrage rapide : Extraire du texte imprimé (OCR) à l’aide de l’API REST Vision par ordinateur et de Node.js

> [!NOTE]
> Si vous extrayez du texte en langue anglaise, envisagez d’utiliser la nouvelle [opération de lecture](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text).

Dans ce guide de démarrage rapide, vous allez extraire le texte imprimé d’une image avec la reconnaissance optique de caractères (OCR) en utilisant l’API REST de Vision par ordinateur. Avec la méthode [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), vous pouvez détecter le texte imprimé dans une image et extraire les caractères reconnus dans un flux de caractères exploitable automatiquement.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- [Node.js](https://nodejs.org) version 4.x ou ultérieure doit être installé.
- [npm](https://www.npmjs.com/) doit être installé.
- Vous devez disposer d’une clé d’abonnement pour la Vision par ordinateur. Vous pouvez obtenir une clé d’essai gratuit auprès de [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vous pouvez également suivre les instructions mentionnées dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Vision par ordinateur et obtenir votre clé. Ensuite, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la chaîne de point de terminaison de la clé et du service, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Création et exécution de l’exemple

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Installez le package [`request`](https://www.npmjs.com/package/request) npm.
   1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
   1. Exécutez la commande suivante :

      ```console
      npm install request
      ```

   1. Une fois que vous avez installé le package, fermez la fenêtre d’invite de commandes.

1. Copiez le code ci-après dans un éditeur de texte.
1. Remplacez éventuellement la valeur de `imageUrl` par l’URL d’une autre image à partir de laquelle vous voulez extraire le texte imprimé.
1. Enregistrez le code dans un fichier avec une extension `.js`. Par exemple : `get-printed-text.js`.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `node` pour exécuter le fichier. Par exemple : `node get-printed-text.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/ocr';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' +
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

// Request parameters.
const params = {
    'language': 'unk',
    'detectOrientation': 'true',
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple analyse et affiche une réponse réussie dans la fenêtre d’invite de commandes, comme dans l’exemple suivant :

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le fichier, puis désinstallez le package `request` npm. Pour désinstaller le package, effectuez les étapes suivantes :

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Exécutez la commande suivante :

   ```console
   npm uninstall request
   ```

3. Une fois que vous avez désinstallé le package, fermez la fenêtre d’invite de commandes.

## <a name="next-steps"></a>Étapes suivantes

À présent, explorez les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit.

> [!div class="nextstepaction"]
> [Explorer l’API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
