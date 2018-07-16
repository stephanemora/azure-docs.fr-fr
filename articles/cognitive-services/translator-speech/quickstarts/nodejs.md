---
title: Démarrage rapide Node.js pour Azure Cognitive Services, API de traduction de conversation Microsoft Translator Speech | Microsoft Docs
description: Obtenez des informations et des exemples de code pour prendre rapidement en main l’API de traduction de conversation Microsoft Translator Speech dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: d469fa008ba8acaf505fa09596dd739d5cc7744c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368436"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>Démarrage rapide pour l’API de traduction de conversation Microsoft Translator Speech avec Node.js 
<a name="HOLTop"></a>

Cet article explique comment utiliser l’API de traduction de conversation Microsoft Translator Speech pour traduire des mots prononcés dans un fichier .wav.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Node.js 6](https://nodejs.org/en/download/) pour exécuter ce code.

Vous devez installer le [package Websocket](https://www.npmjs.com/package/websocket) pour Node.js.

Vous devez disposer d’un fichier .wav nommé « speak.wav » dans le même dossier que le fichier exécutable que vous compilez à partir du code ci-dessous. Ce fichier .wav doit être au format PCM standard 16 bits, 16 kHz, monocanal. Vous pouvez obtenir ce type de fichier .wav à partir de [l’API de traduction de texte Translator Text](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec **l’API de traduction de conversation Microsoft Translator Speech**. Vous avez besoin d’une clé d’abonnement payant que vous pouvez trouver sur votre [tableau de bord Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traduction vocale

Le code suivant traduit un énoncé d’une langue à une autre.

1. Créez un projet Node.js dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Translator Text Speak API. See:
http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio input is finished. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Réponse de la traduction vocale**

Si l’opération réussit, un fichier nommé « speak2.wav » est créé. Le fichier contient la traduction des mots prononcés dans « speak.wav ».

[Revenir en haut](#HOLTop)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel sur l’API de traduction de conversation Translator Speech](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Voir aussi 

[API de traduction de conversation Microsoft Translator Speech](../overview.md)
[Informations de référence sur l’API](http://docs.microsofttranslator.com/speech-translate.html)
