---
title: Démarrage rapide API Vision par ordinateur avec Node.js - OCR | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous allez extraire le texte imprimé d’une image à l’aide de l’API Vision par ordinateur avec Node.js dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 73fe11800d7141c9e6e7272a0d008d2bcdf6d6af
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770149"
---
# <a name="quickstart-extract-printed-text-ocr---rest-nodejs"></a>Démarrage rapide : Extraire le texte imprimé (OCR) - REST, Node.js

Dans ce démarrage rapide, vous allez extraire le texte imprimé selon une technique également appelée reconnaissance optique des caractères (OCR) à partir d’une image, à l’aide de l’API Vision par ordinateur.

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [How to obtain subscription keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Obtention de clés d’abonnement).

## <a name="ocr-request"></a>Requête OCR

Avec la [méthode OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), vous pouvez détecter le texte imprimé dans une image et extraire les caractères reconnus dans un flux de caractères exploitable automatiquement.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez la valeur `uriBase` par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
1. Si vous le souhaitez, remplacez la valeur `imageUrl` par l’image que vous souhaitez analyser.
1. Enregistrez le fichier avec une extension `.js`.
1. Ouvrez l’invite de commandes Node.js et exécutez le fichier, par exemple : `node myfile.js`.

Cet exemple utilise le package npm [request](https://www.npmjs.com/package/request).

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr';

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

## <a name="ocr-response"></a>Réponse OCR

En cas de réussite, les résultats OCR renvoyés incluent du texte, ainsi qu’un rectangle englobant pour les régions, les lignes et les mots, par exemple :

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

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit. Pour tester rapidement les API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
