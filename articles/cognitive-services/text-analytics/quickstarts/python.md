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
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: 6edcb4501feb0ac2911fed075ed4866aa267a80e
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893076"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Démarrage rapide : Utilisation de Python pour appeler le service cognitif Analyse de texte 
<a name="HOLTop"></a>

Cette procédure pas à pas vous montre comment [détecter la langue](#Detect), [analyser les sentiments](#SentimentAnalysis) et [extraire les expressions clés](#KeyPhraseExtraction) à l’aide des [API Analyse de texte ](//go.microsoft.com/fwlink/?LinkID=759711) avec Python.

Vous pouvez exécuter cet exemple à partir de la ligne de commande comme un notebook Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

### <a name="command-line"></a>Ligne de commande

Vous devrez peut-être mettre à jour [IPython](https://ipython.org/install.html), le noyau de Jupyter :
```bash
pip install --upgrade IPython
```

Vous devrez peut-être mettre à jour la bibliothèque [Requests](http://docs.python-requests.org/en/master/) :
```bash
pip install requests
```

Consultez les [définitions des API](//go.microsoft.com/fwlink/?LinkID=759346) pour accéder à la documentation technique des API.

## <a name="prerequisites"></a>Prérequis

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

* Le [point de terminaison et la clé d’accès](../How-tos/text-analytics-how-to-access-key.md) générée pendant le processus d’inscription.

* Les importations suivantes, la clé d’abonnement et `text_analytics_base_url` sont utilisés pour tous les guides de démarrage rapide ci-dessous. Ajoutez les importations.

    ```python
    import requests
    # pprint is pretty print (formats the JSON)
    from pprint import pprint
    from IPython.display import HTML
    ```
    
    Ajoutez ces lignes, puis remplacez `subscription_key` par une clé d’abonnement valide obtenue précédemment.
    
    ```python
    subscription_key = '<ADD KEY HERE>'
    assert subscription_key
    ```
    
    Ensuite, ajoutez cette ligne, puis vérifiez que la région dans `text_analytics_base_url` correspond à celle utilisée pendant la configuration du service. Si vous utilisez une clé d’essai gratuit, vous n’avez rien à changer.
    
    ```python
    text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
    ```

<a name="Detect"></a>

## <a name="detect-languages"></a>Détecter des langues

L’API Détection de langue détecte la langue d’un document texte à l’aide de la [méthode Detect Language](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). Le point de terminaison de service de l’API Détection de langue de votre région est disponible via l’URL suivante :

```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

```url
https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages
```

La charge utile de l’API se compose d’une liste de `documents` qui contiennent un `id` et un attribut `text`. L’attribut `text` stocke le texte à analyser. 

Remplacez le dictionnaire `documents` par n’importe quel autre texte pour la détection de la langue.

```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Les quelques lignes de code suivantes appellent l’API Détection de langue à l’aide de la bibliothèque `requests` dans Python, afin de déterminer la langue dans les documents.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

Les lignes de code suivantes restituent les données JSON sous la forme d’une table HTML.

```python
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

Réponse JSON correcte :

```json
    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analyser les sentiments

L’API Analyse des sentiments détecte les sentiments (positifs ou négatifs) d’un ensemble d’enregistrements de texte à l’aide de la [méthode Sentiment](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). L’exemple suivant évalue deux documents, l’un en anglais, l’autre en espagnol.

Le point de terminaison de service pour l’analyse des sentiments est disponible dans votre région via l’URL suivante :

```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```
    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment

Comme avec l’exemple de détection de langue, le service est fourni avec un dictionnaire doté d’une clé `documents` qui se compose d’une liste de documents. Chaque document est un tuple constitué de l’`id`, du `text` à analyser et de la `language` du texte. Vous pouvez utiliser l’API Détection de langue de la section précédente pour renseigner ce champ.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

L’API Sentiment peut maintenant être utilisée pour analyser les documents afin de connaître les sentiments qu’ils contiennent.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

Réponse JSON correcte :

```json
{'documents': [{'id': '1', 'score': 0.7673527002334595},
                {'id': '2', 'score': 0.18574094772338867},
                {'id': '3', 'score': 0.5}],
    'errors': []}
```

Le score de sentiment d’un document est compris entre 0.0 et 1.0 ; un score supérieur indique un sentiment plus positif.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extraire des expressions clés

L’API Extraction de phrases clés extrait des phrases clés d’un document texte à l’aide de la [méthode Phrases clés](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Dans cette section de la procédure pas à pas, vous allez extraire les phrases clés pour les documents en anglais et en espagnol.

Le point de terminaison de service pour le service d’extraction de phrases clés est accessible via l’URL suivante :

```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```
    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases

La collection de documents est la même que celle utilisée pour l’analyse des sentiments.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

L’objet JSON peut être restitué sous la forme d’une table HTML via les lignes de code suivantes :

```python
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

Les quelques lignes de code suivantes appellent l’API Détection de langue à l’aide de la bibliothèque `requests` dans Python, afin de déterminer la langue dans les documents.
```python
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

Réponse JSON correcte :
```json
{'documents': [
    {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
    {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
    {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
    {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
    'errors': []
}
```

## <a name="identify-entities"></a>Identification d’entités

L’API Entités identifie les entités bien connues dans un document texte à l’aide de la [méthode Entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634). L’exemple suivant identifie les entités dans les documents en anglais.

Le point de terminaison de service pour le service de liaison d’entités est accessible via l’URL suivante :

```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities

La collection de documents se trouve ci-dessous :

```python
documents = {'documents' : [
  {'id': '1', 'text': 'Jeff bought three dozen eggs because there was a 50% discount.'},
  {'id': '2', 'text': 'The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.'}
]}
```
Les documents peuvent maintenant être envoyés à l’API Analyse de texte pour recevoir la réponse.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

Réponse JSON correcte :
```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Forum Aux Questions (FAQ)](../text-analytics-resource-faq.md)
