---
title: 'Démarrage rapide : Convertir un script de texte - Traduction de texte Translator Text, Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous convertissez le texte d’un script d’une langue à une autre à l’aide de l’API de traduction de texte Translator Text avec Python.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 24fe0dc23703bdca4ce74ba1a527990a70ac2ffc
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122109"
---
# <a name="quickstart-transliterate-text-with-python"></a>Démarrage rapide : Translittérer du texte avec Python

Dans ce démarrage rapide, vous convertissez du texte dans la langue d’un script vers une autre à l’aide de l’API de traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

Pour exécuter ce code, vous devez disposer de [Python 3.x](https://www.python.org/downloads/).

Pour utiliser l’API de traduction de texte Translator Text, vous avez également besoin d’une clé d’abonnement. Consultez [Comment s’inscrire à l’API de traduction de texte Translator Text](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Requête Translittérer

Ce qui suit convertit le texte dans la langue d’un script vers une autre en utilisant la méthode [Translittérer](./reference/v3-0-transliterate.md).

1. Créez un nouveau projet Python dans votre éditeur de code favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `subscriptionKey` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = '&language=ja&fromScript=jpan&toScript=latn';

# Transliterate "good afternoon".
text = 'こんにちは'

def transliterate (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = transliterate (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="transliterate-response"></a>Réponse Translittérer

Une réponse correcte est renvoyée au format JSON, comme dans l’exemple suivant :

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez l’exemple de code pour ce démarrage rapide et d’autres, y compris la traduction et l’identification de la langue, ainsi que d’autres exemples de projets de l’API de traduction de texte Translator Text sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
