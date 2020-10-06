---
title: 'Démarrage rapide : utilisation de Python pour appeler l’API Analyse de texte'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment utiliser Python pour obtenir des informations et des exemples de code afin de vous aider à commencer rapidement à utiliser l’API Analyse de texte dans Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 0ef870b6b2d3b88b13c16c8c2acbfcee7ed551c1
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527239"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Utiliser l'API REST Python pour appeler le service cognitif Analyse de texte 
<a name="HOLTop"></a>

Utilisez ce guide de démarrage rapide pour commencer l’analyse de la langue avec l’API REST Analyse de texte et Python. Cet article montre comment [détecter la langue](#Detect), [analyser les sentiments](#SentimentAnalysis), [extraire les expressions clés](#KeyPhraseExtraction) et [identifier les entités liées](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Prérequis

* [Python 3.x](https://python.org)

* Bibliothèque de requêtes Python
    
    Vous pouvez installer la bibliothèque avec cette commande :

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python dans votre éditeur ou environnement de développement intégré favori. Ajoutez les importations suivantes à votre fichier.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Créez des variables pour le point de terminaison et la clé d’abonnement Azure de votre ressource.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

Les sections suivantes expliquent comment appeler chacune des fonctionnalités de l’API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Détecter des langues

Ajoutez `/text/analytics/v3.0/languages` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

La charge utile de l’API se compose d’une liste de `documents`, correspondant à des tuples qui contiennent un `id` et un attribut `text`. L’attribut `text` stocke le texte à analyser, et le `id` peut être n’importe quelle valeur. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Output

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analyser les sentiments

Pour détecter le sentiment (positif ou négatif) d’un jeu de documents, ajoutez `/text/analytics/v3.0/sentiment` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Comme avec l’exemple de détection de langue, créez un dictionnaire doté d’une clé `documents` qui se compose d’une liste de documents. Chaque document est un tuple constitué de l’`id`, du `text` à analyser et de la `language` du texte. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Output

Le score de sentiment d’un document est compris entre 0.0 et 1.0 ; un score supérieur indique un sentiment plus positif.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extraire des expressions clés
 
Pour extraire les expressions clés d’un jeu de documents, ajoutez `/text/analytics/v3.0/keyPhrases` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

Cette collection de documents est la même que celle utilisée pour l’exemple d’analyse des sentiments.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Output

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identification d’entités

Pour identifier des entités connues (personnes, lieux et objets) dans des documents texte, ajoutez `/text/analytics/v3.0/entities/recognition/general` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Créez une collection de documents, comme dans les exemples précédents. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`.

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Output

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
