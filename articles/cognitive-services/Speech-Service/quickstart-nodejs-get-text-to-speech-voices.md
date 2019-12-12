---
title: 'Démarrage rapide : Répertorier les voix de la synthèse vocale, Node.js – Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronales pour une région/un point de terminaison avec Node.js. La liste est retournée au format JSON, et la disponibilité des voix varie selon la région.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976568"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Démarrage rapide : Obtenir la liste des voix de synthèse vocale à l’aide de Node.js

Dans ce guide de démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronales pour une région/un point de terminaison avec Node.js. La liste est retournée au format JSON, et la disponibilité des voix varie selon la région. Pour obtenir la liste des régions prises en charge, consultez [Régions](regions.md).

Ce guide de démarrage rapide demande un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource du service Speech. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](get-started.md) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Node 8.12.x ou version ultérieure](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Créer un projet et requérir des dépendances

Créez un projet Node.js à l’aide de votre éditeur ou IDE favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `npm install request request-promise`.

## <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

L’API REST Synthèse vocale nécessite un jeton d’accès pour l’authentification. Pour obtenir un jeton d’accès, un échange est nécessaire. Cette fonction échange votre clé d’abonnement du service Speech contre un jeton d’accès utilisant le point de terminaison `issueToken`.

Cet exemple suppose que votre abonnement du service Speech se situe dans la région USA Ouest. Si vous utilisez une autre région, mettez à jour la valeur de `uri`. Pour obtenir la liste complète, consultez [Régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiez ce code dans votre projet :

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Pour plus d’informations sur l’authentification, consultez [S’authentifier avec un jeton d’accès](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Dans la section suivante, nous allons créer la fonction permettant d’obtenir la liste des voix et enregistrer la sortie JSON dans le fichier.

## <a name="make-a-request-and-save-the-response"></a>Effectuer une requête et enregistrer la réponse

Ici, vous allez créer la requête et enregistrer la liste de voix retournée. Cet exemple part du principe que vous utilisez le point de terminaison USA Ouest. Si votre ressource est inscrite dans une autre région, veillez à mettre à jour la valeur `uri`. Pour plus d’informations, consultez [Régions du service Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ajoutez ensuite les en-têtes nécessaires pour la requête. Enfin, vous envoyez une requête au service. Si la requête aboutit et qu’un code d’état 200 est retourné, la réponse est écrite dans un fichier.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Assemblage

Vous avez presque terminé. La dernière étape consiste à créer une fonction asynchrone. Cette fonction lira votre clé d’abonnement à partir d’une variable d’environnement, obtiendra un jeton, attendra que la requête aboutisse, puis écrira la réponse JSON dans le fichier.

Si les variables d’environnement ne vous sont pas familières ou si vous préférez tester avec votre clé abonnement codée en dur sous forme de chaîne, remplacez `process.env.SPEECH_SERVICE_KEY` par votre clé d’abonnement sous forme de chaîne.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Node.js sur GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Voir aussi

* [Référence sur l’API conversion de texte par synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Création de polices de voix personnalisée](how-to-customize-voice-font.md)
* [Enregistrer des échantillons vocaux pour créer une voix personnalisée](record-custom-voice-samples.md)
