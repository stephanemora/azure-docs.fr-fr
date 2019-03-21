---
title: 'Démarrage rapide : Générer une miniature - REST, Node.js'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une miniature d’une image en utilisant l’API Vision par ordinateur avec Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c10be50adbfae559873da87fffe4cdb10bc350ef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57875836"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-nodejs-in-computer-vision"></a>Démarrage rapide : Générer une miniature à l’aide de l’API REST et Node.js dans Vision par ordinateur

Dans ce guide de démarrage rapide, vous générez une miniature d’une image en utilisant l’API REST de Vision par ordinateur. Avec la méthode [Obtenir une miniature](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), vous pouvez générer une miniature d’une image. Vous spécifiez la hauteur et la largeur, qui peuvent différer des proportions de l’image d’entrée. L’API Vision par ordinateur utilise le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage en fonction de cette région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- [Node.js](https://nodejs.org) version 4.x ou ultérieure doit être installé.
- [npm](https://www.npmjs.com/) doit être installé.
- Vous devez avoir une clé d’abonnement pour Vision par ordinateur. Pour obtenir une clé d’abonnement, consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Créer et exécuter l’exemple

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Installez le package [`request`](https://www.npmjs.com/package/request) npm.
   1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
   1. Exécutez la commande suivante :

      ```console
      npm install request
      ```

   1. Une fois que vous avez installé le package, fermez la fenêtre d’invite de commandes.

1. Copiez le code ci-après dans un éditeur de texte.
1. Modifiez le code comme ci-dessous :
    1. Remplacez la valeur de `subscriptionKey` par votre clé d’abonnement.
    1. Si nécessaire, remplacez la valeur de `uriBase` par l’URL du point de terminaison de la méthode [Obtenir la miniature](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) dans la région Azure où vous avez obtenu vos clés d’abonnement.
    1. Remplacez éventuellement la valeur de `imageUrl` par l’URL d’une autre image que vous voulez analyser.
1. Enregistrez le code dans un fichier avec une extension `.js`. Par exemple : `get-thumbnail.js`.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `node` pour exécuter le fichier. Par exemple : `node get-thumbnail.js`.

```javascript
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
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
});
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse réussie est retournée sous forme de données binaires qui représentent les données image de la miniature. Si la requête échoue, la réponse s’affiche dans la fenêtre de console. La réponse associée à l’échec de la requête contient un code d’erreur et un message indiquant la cause du problème.

## <a name="next-steps"></a>Étapes suivantes

Explorez l’API Vision par ordinateur utilisée pour analyser une image, détecter des célébrités et des monuments, créer une miniature et extraire le texte imprimé ou manuscrit. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorer l’API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
