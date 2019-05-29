---
title: 'Démarrage rapide : Utilisation de Python pour appeler l’API Analyse de texte'
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations et des exemples de code pour vous aider à commencer à utiliser rapidement l’API Analyse de texte dans Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/09/2019
ms.author: aahi
ms.openlocfilehash: 9ae894bee803c60b56a1bfacd5667f355aa44d2b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799999"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Démarrage rapide : Utilisation de l’API REST Python pour appeler le service cognitif Analyse de texte 
<a name="HOLTop"></a>

Utilisez ce guide de démarrage rapide pour commencer l’analyse de la langue avec l’API REST Analyse de texte et Python. Cet article montre comment [détecter la langue](#Detect), [analyser les sentiments](#SentimentAnalysis), [extraire les expressions clés](#KeyPhraseExtraction) et [identifier les entités liées](#Entities).

Consultez les [définitions des API](//go.microsoft.com/fwlink/?LinkID=759346) pour accéder à la documentation technique des API.

## <a name="prerequisites"></a>Prérequis

* [Python 3.x](https://python.org)

* Le [point de terminaison et la clé d’accès](../How-tos/text-analytics-how-to-access-key.md) générée pendant le processus d’inscription.

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
from IPython.display import HTML
```

Créez des variables pour votre clé d’abonnement et le point de terminaison de l’API REST Analyse de texte. Vérifiez que la région du point de terminaison correspond à celle que vous avez utilisée lors de la connexion (par exemple `westcentralus`). Si vous utilisez une clé d’essai gratuit, vous n’avez rien à changer.
    
```python
subscription_key = "<ADD YOUR KEY HERE>"
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
```

Les sections suivantes expliquent comment appeler chacune des fonctionnalités de l’API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Détecter des langues

Ajoutez `languages` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = text_analytics_base_url + "languages"
```

La charge utile de l’API se compose d’une liste de `documents`, correspondant à des tuples qui contiennent un `id` et un attribut `text`. L’attribut `text` stocke le texte à analyser, et le `id` peut être n’importe quelle valeur. 

```python
documents = { "documents": [
    { "id": "1", "text": "This is a document written in English." },
    { "id": "2", "text": "Este es un document escrito en Español." },
    { "id": "3", "text": "这是一个用中文写的文件" }
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Sortie

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analyser les sentiments

Pour détecter le sentiment (positif ou négatif) d’un jeu de documents, ajoutez `sentiment` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = text_analytics_base_url + "sentiment"
```

Comme avec l’exemple de détection de langue, créez un dictionnaire doté d’une clé `documents` qui se compose d’une liste de documents. Chaque document est un tuple constitué de l’`id`, du `text` à analyser et de la `language` du texte. 

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Sortie

Le score de sentiment d’un document est compris entre 0.0 et 1.0 ; un score supérieur indique un sentiment plus positif.

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[

  ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extraire des expressions clés
 
Pour extraire les expressions clés d’un jeu de documents, ajoutez `keyPhrases` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = text_analytics_base_url + "keyPhrases"
```

Cette collection de documents est la même que celle utilisée pour l’exemple d’analyse des sentiments.

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Sortie

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identification d’entités

Pour identifier des entités connues (personnes, lieux et objets) dans des documents texte, ajoutez `entities` au point de terminaison de base d’Analyse de texte pour former l’URL de détection de langue. Par exemple : `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`
    
```python
entities_url = text_analytics_base_url + "entities"
```

Créez une collection de documents, comme dans les exemples précédents. 

```python
documents = {"documents" : [
  {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

Utilisez la bibliothèque de requêtes pour envoyer les documents à l’API. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` et envoyez la requête avec `requests.post()`.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
```

### <a name="output"></a>Sortie

```json
{'documents': [{'id': '1',
   'entities': [{'name': 'Microsoft',
     'matches': [{'wikipediaScore': 0.502357972145024,
       'entityTypeScore': 1.0,
       'text': 'Microsoft',
       'offset': 0,
       'length': 9}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Microsoft',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Microsoft',
     'bingId': 'a093e9b9-90f5-a3d5-c4b8-5855e1b01f85',
     'type': 'Organization'},
    {'name': 'Bill Gates',
     'matches': [{'wikipediaScore': 0.5849375085784292,
       'entityTypeScore': 0.999847412109375,
       'text': 'Bill Gates',
       'offset': 25,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Bill Gates',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Bill_Gates',
     'bingId': '0d47c987-0042-5576-15e8-97af601614fa',
     'type': 'Person'},
    {'name': 'Paul Allen',
     'matches': [{'wikipediaScore': 0.5314163053043621,
       'entityTypeScore': 0.9988409876823425,
       'text': 'Paul Allen',
       'offset': 40,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Paul Allen',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Paul_Allen',
     'bingId': 'df2c4376-9923-6a54-893f-2ee5a5badbc7',
     'type': 'Person'},
    {'name': 'April 4',
     'matches': [{'wikipediaScore': 0.37312706493069636,
       'entityTypeScore': 0.8,
       'text': 'April 4',
       'offset': 54,
       'length': 7}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'April 4',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/April_4',
     'bingId': '52535f87-235e-b513-54fe-c03e4233ac6e',
     'type': 'Other'},
    {'name': 'April 4, 1975',
     'matches': [{'entityTypeScore': 0.8,
       'text': 'April 4, 1975',
       'offset': 54,
       'length': 13}],
     'type': 'DateTime',
     'subType': 'Date'},
    {'name': 'BASIC',
     'matches': [{'wikipediaScore': 0.35916049097766867,
       'entityTypeScore': 0.8,
       'text': 'BASIC',
       'offset': 89,
       'length': 5}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'BASIC',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/BASIC',
     'bingId': '5b16443d-501c-58f3-352e-611bbe75aa6e',
     'type': 'Other'},
    {'name': 'Altair 8800',
     'matches': [{'wikipediaScore': 0.8697256853652899,
       'entityTypeScore': 0.8,
       'text': 'Altair 8800',
       'offset': 116,
       'length': 11}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Altair 8800',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Altair_8800',
     'bingId': '7216c654-3779-68a2-c7b7-12ff3dad5606',
     'type': 'Other'}]}],
 'errors': []}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
