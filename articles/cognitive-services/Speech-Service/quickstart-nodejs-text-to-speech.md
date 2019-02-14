---
title: 'Démarrage rapide : Convertir du texte par synthèse vocale, Node.js - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à convertir du texte par synthèse vocale à l’aide de Node.js et de l’API REST Synthèse vocale. L’exemple de texte inclus dans ce guide est structuré en tant que langage de balisage de synthèse vocale (SSML). Cela vous permet de choisir la voix et la langue de la réponse de la reconnaissance vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 7faa69e4adf96af7f7df9724521ee5ee1cacaad1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861644"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Démarrage rapide : Convertir du texte par synthèse vocale à l’aide de Node.js

Dans ce guide de démarrage rapide, vous allez apprendre à convertir du texte par synthèse vocale à l’aide de Node.js et de l’API REST Synthèse vocale. Le corps de la requête figurant dans ce guide est structuré en tant que [Langage de balisage de synthèse vocale (SSML)](speech-synthesis-markup.md), ce qui vous permet de choisir la voix et la langue de la réponse.

Ce guide de démarrage rapide exige un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource du service Speech. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](get-started.md) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Node 8.12.x ou version ultérieure](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Créer un projet et requérir des dépendances

Créez un projet Node.js à l’aide de votre éditeur ou IDE favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `tts.js`.

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `npm install request readline-sync`.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Définir la clé d’abonnement et créer une invite pour la conversion de texte par synthèse vocale (TTS)

Dans les sections suivantes, vous allez créer des fonctions pour gérer l’autorisation, appeler l’API Synthèse vocale et valider la réponse. Commençons par ajouter une clé d’abonnement et créer une invite pour la saisie de texte.

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

L’API REST Synthèse vocale nécessite un jeton d’accès pour l’authentification. Pour obtenir un jeton d’accès, un échange est nécessaire. Cet exemple échange votre clé d’abonnement du service Speech avec un jeton accès à l’aide du point de terminaison `issueToken`.

Cette fonction prend deux arguments, votre clé d’abonnement aux services vocaux et une fonction de rappel. Une fois que la fonction a obtenu un jeton d’accès, elle transmet la valeur à la fonction de rappel. Dans la section suivante, nous allons créer la fonction pour appeler l’API de reconnaissance vocale et enregistrer la réponse vocale synthétisée.

Cet exemple suppose que votre abonnement du service Speech se situe dans la région USA Ouest. Si vous utilisez une autre région, mettez à jour la valeur de `uri`. Pour obtenir la liste complète, consultez [Régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiez ce code dans votre projet :

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> Pour plus d’informations sur l’authentification, consultez [S’authentifier avec un jeton d’accès](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Effectuer une requête et enregistrer la réponse

Ici, vous allez générer la requête de l’API de reconnaissance vocale et enregistrer la réponse de la reconnaissance vocale. Cet exemple part du principe que vous utilisez le point de terminaison USA Ouest. Si votre ressource est inscrite dans une autre région, veillez à mettre à jour la valeur `uri`. Pour plus d’informations, consultez [Régions du service Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ensuite, vous devez ajouter les en-têtes nécessaires pour la requête. Veillez à mettre à jour `User-Agent` avec le nom de votre ressource (située dans le portail Azure), puis définissez `X-Microsoft-OutputFormat` sur la sortie audio de votre choix. Pour obtenir la liste complète des formats de sortie, consultez [Sorties audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Construisez ensuite le corps de la requête en utilisant le langage de balisage de synthèse vocale (SSML). Cet exemple définit la structure et utilise l’entrée `text` créée précédemment.

>[!NOTE]
> Cet exemple utilise la police de la voix `JessaRUS`. Pour obtenir la liste complète des voix/langues fournies par Microsoft, consultez [Prise en charge de la langue](language-support.md).
> Si vous êtes intéressé par la création d’une voix unique, reconnaissable pour votre marque, consultez [Création de polices de voix personnalisée](how-to-customize-voice-font.md).

Enfin, vous envoyez une requête au service. Si la requête réussit et qu’un code d’état 200 est retourné, la réponse de la reconnaissance vocale est écrite en tant que `sample.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
      .att('version', '1.0')
      .att('xml:lang', 'en-us')
      .ele('voice')
      .att('xml:lang', 'en-us')
      .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
      .txt(text)
      .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>Assemblage

Vous avez presque terminé. La dernière étape consiste à appeler la fonction `textToSpeech`.

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application de synthèse vocale. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
node tts.js
```

Quand vous y êtes invité, tapez ce que vous souhaitez convertir par synthèse vocale. En cas de réussite, le fichier vocal se trouve dans votre dossier de projet. Vous pouvez procéder à sa lecture à l’aide du lecteur multimédia de votre choix.

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Node.js sur GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Voir aussi

* [Référence sur l’API conversion de texte par synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Création de polices de voix personnalisée](how-to-customize-voice-font.md)
* [Enregistrer des échantillons vocaux pour créer une voix personnalisée](record-custom-voice-samples.md)
