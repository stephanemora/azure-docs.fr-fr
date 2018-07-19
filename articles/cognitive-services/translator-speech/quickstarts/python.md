---
title: Démarrage rapide Python pour Azure Cognitive Services, API de traduction de conversation Microsoft Translator Speech | Microsoft Docs
description: Obtenez des informations et des exemples de code pour commencer rapidement à utiliser l’API de traduction de conversation Microsoft Translator Speech dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: 40535f400bc5359019e89320489721bf481c1210
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124825"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Démarrage rapide pour l’API de traduction de conversation Microsoft Translator Speech avec Python 
<a name="HOLTop"></a>

Cet article explique comment utiliser l’API de traduction de conversation Microsoft Translator Speech pour traduire des mots prononcés dans un fichier .wav.

## <a name="prerequisites"></a>Prérequis

Pour exécuter ce code, vous devez disposer de [Python 3.x](https://www.python.org/downloads/).

Vous devez installer le [package websocket-client](https://pypi.python.org/pypi/websocket-client) pour Python.

Vous devez disposer d’un fichier .wav nommé « speak.wav » dans le même dossier que le fichier exécutable que vous compilez à partir du code ci-dessous. Ce fichier .wav doit être au format PCM standard 16 bits, 16kHz, monocanal.

Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec **l’API de traduction de conversation Microsoft Translator Speech**. Vous devez disposer d’une clé d’abonnement payant à partir de votre [tableau de bord Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traduction vocale

Le code suivant traduit un énoncé d’une langue en une autre.

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
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
