---
title: 'Démarrage rapide : Détecter la langue du texte, Python – API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez apprendre à identifier la langue du texte fourni à l’aide de Python et de l’API REST de traduction de texte Translator Text.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: erhopf
ms.openlocfilehash: 564a12de2a0823372ce267f9ff2759ab17199a80
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086034"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-python"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text pour détecter la langue du texte à l’aide de Python

Dans ce démarrage rapide, vous allez apprendre à détecter la langue d’un texte donné à l’aide de Python et de l’API REST de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Python 2.7.x ou 3.x
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `detect.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `pip install requests uuid`.

Le premier commentaire ordonne à votre interpréteur Python d’utiliser l’encodage UTF-8. Puis, les modules requis sont importés pour lire votre clé d’abonnement depuis une variable d’environnement, élaborer la requête http, créer un identificateur unique et gérer la réponse JSON rendue par l’API Translator Text.

## <a name="set-the-subscription-key-base-url-and-path"></a>Définissez la clé d’abonnement, une URL de base et un chemin d’accès

Cet exemple tentera de lire votre clé d’abonnement Translator Text depuis la variable d’environnement `TRANSLATOR_TEXT_KEY`. Si vous n’êtes pas familiarisé avec les variables d’environnement, vous pouvez définir `subscriptionKey` en tant que chaîne et commentez l’instruction conditionnelle.

Copiez ce code dans votre projet :

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Il y existe actuellement un point de terminaison disponible pour Translator Text, et il est défini comme des `base_url`. `path` définit le chemin des `detect` et reconnait que nous souhaitons atteindre la version 3 de l’API.

>[!NOTE]
> Pour plus d’informations concernant les points de terminaison, les itinéraires et les paramètres de la requête, consultez [API de traduction de texte Translator Text 3.0 : Détecter](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/detect?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Ajouter les en-têtes

La manière la plus simple d’authentifier une requête consiste à transmettre votre clé d’abonnement comme `Ocp-Apim-Subscription-Key`en-tête. C’est ce que nous avons fait dans ce modèle. Vous pouvez aussi échanger votre clé d’abonnement pour un jeton d’accès et transmettre le jeton d’accès en tant qu’`Authorization`en-tête pour valider votre requête. Pour en savoir plus, consultez [Authentification](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copiez cet extrait de code dans votre projet :

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-detect-text-language"></a>Créez une requête pour détecter la langue du texte

Définissez la chaîne (ou les chaînes) pour lesquelles vous souhaitez détecter le langage :

```python
# You can pass more than one object in body.
body = [{
    'text': 'Salve, mondo!'
}]
```

Ensuite, nous allons créer une requête POST à l’aide du module `requests`. Il tient compte de trois arguments : l’URL concaténée, les en-têtes de requête et le corps de la demande :

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Imprimer la réponse

La dernière étape consiste à imprimer les résultats. Cet extrait de code agrémente les résultats en triant les clés, en configurant la mise en retrait et en déclarant des séparateurs d’élément et de clés.

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Assemblage

Voilà, vous avez installé un programme simple qui appellera l’API de traduction de texte Translator Text et enverra une réponse JSON. Il est maintenant temps d’exécuter votre programme :

```console
python detect.py
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exemple de réponse

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez codé en dur votre clé d’abonnement dans votre programme, veillez à supprimer la clé d’abonnement une fois ce démarrage rapide terminé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Voir aussi

En plus de la détection des langues, découvrez comment utiliser l’API de traduction de texte Translator Text pour :

* [Traduire du texte](quickstart-python-translate.md)
* [Translittérer du texte](quickstart-python-transliterate.md)
* [Obtenir des traductions alternatives](quickstart-python-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-python-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-python-sentences.md)
