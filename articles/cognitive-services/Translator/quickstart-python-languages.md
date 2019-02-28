---
title: 'Démarrage rapide : Obtenir la liste des langues prises en charge, Python - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez obtenir la liste des langues prises en charge pour la traduction, la translittération et la recherche dans le dictionnaire, ainsi que des exemples d’utilisation de l’API de traduction de texte Translator Text avec Python.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 705bc49cef13ae80d648ca59b50021250dede3bd
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736768"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text pour obtenir la liste des langues prises en charge à l’aide de Python

Dans ce démarrage rapide, vous allez apprendre à effectuer une requête GET qui retourne la liste des langues prises en charge à l’aide de Python et de l’API REST Traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Python 2.7.x ou 3.x

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `get-languages.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `pip install requests uuid`.

Le premier commentaire ordonne à votre interpréteur Python d’utiliser l’encodage UTF-8. Puis, les modules requis sont importés pour lire votre clé d’abonnement depuis une variable d’environnement, élaborer la requête http, créer un identificateur unique et gérer la réponse JSON rendue par l’API Translator Text.

## <a name="set-the-base-url-and-path"></a>Définir l’URL de base et le chemin

Le point de terminaison global de l’API de traduction de texte Translator Text est défini sur `base_url`. `path` définit le chemin des `languages` et reconnait que nous souhaitons atteindre la version 3 de l’API.

>[!NOTE]
> Pour plus d’informations sur les points de terminaison, les routes et les paramètres de requête, consultez [API de traduction de texte Translator Text 3.0 : Langues](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Ajouter les en-têtes

La requête d’obtention des langues prises en charge ne nécessite pas d’authentification. Définissez `Content-type` sur `application/json` et ajoutez `X-ClientTraceId` pour identifier votre requête de manière unique.

Copiez cet extrait de code dans votre projet :

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Créer une requête pour obtenir la liste des langues prises en charge

Créons une requête GET à l’aide du module `requests`. Il tient compte de deux arguments : l’URL concaténée et les en-têtes de requête :

```python
request = requests.get(constructed_url, headers=headers)
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
python get-languages.py
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exemple de réponse

Cet exemple a été tronqué pour afficher un extrait du résultat :

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez codé en dur votre clé d’abonnement dans votre programme, veillez à supprimer la clé d’abonnement une fois ce démarrage rapide terminé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Voir aussi

Apprenez à utiliser l’API de traduction de texte Translator Text pour :

* [Traduire le texte](quickstart-python-translate.md)
* [Translittérer du texte](quickstart-python-transliterate.md)
* [Identifier la langue par entrée](quickstart-python-detect.md)
* [Obtenir des traductions alternatives](quickstart-python-dictionary.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-python-sentences.md)
