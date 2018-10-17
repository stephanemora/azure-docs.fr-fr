---
title: 'Démarrage rapide : Convertir un script de texte - Traduction de texte Translator Text, Ruby'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez convertir le texte d’un script d’une langue à une autre à l’aide de l’API de traduction de texte Translator Text avec Ruby.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: 6595e486a93811fdded21050c39e99d4f7e5a760
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121751"
---
# <a name="quickstart-transliterate-text-with-ruby"></a>Démarrage rapide : Translittérer du texte avec Ruby

Dans ce démarrage rapide, vous convertissez du texte dans la langue d’un script vers une autre à l’aide de l’API de traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

[Ruby 2.4](https://www.ruby-lang.org/en/downloads/) (ou une version ultérieure) est nécessaire pour exécuter ce code.

Pour utiliser l’API de traduction de texte Translator Text, vous avez également besoin d’une clé d’abonnement. Consultez [Comment s’inscrire à l’API de traduction de texte Translator Text](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Requête Translittérer

Ce qui suit convertit le texte dans une langue d’un script vers un autre en utilisant la méthode [Translittérer](./reference/v3-0-transliterate.md).

1. Créez un nouveau projet Ruby dans votre éditeur de code favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = "&language=ja&fromScript=jpan&toScript=latn";

uri = URI (host + path + params)

# Transliterate "good afternoon".
text = 'こんにちは'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
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
> [Explorer des exemples Ruby sur GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)
