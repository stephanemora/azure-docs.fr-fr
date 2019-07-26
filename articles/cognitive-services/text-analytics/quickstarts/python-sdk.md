---
title: "Démarrage rapide : Appeler le service Analyse de texte à l'aide du kit de développement logiciel (SDK) Python"
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations et des exemples de code pour vous aider à commencer à utiliser rapidement l’API Analyse de texte dans Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: c24979d9aef74b6cc840427a010b9ce70f2c0b8a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356958"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Démarrage rapide : Appeler le service Analyse de texte à l'aide du kit de développement logiciel (SDK) Python 
<a name="HOLTop"></a>

Utilisez ce démarrage rapide pour commencer l’analyse du langage avec le SDK Analyse de texte pour Python. Si l’API REST Analyse de texte est compatible avec la plupart des langages de programmation, le kit SDK offre quant à lui un moyen facile d’intégrer le service à vos applications, sans sérialisation ni désérialisation JSON. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Prérequis

* [Python 3.x](https://www.python.org/)

* Kit de développement logiciel (SDK) Python [Analyse de texte](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) Vous pouvez installer le package avec :

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Vous devez également avoir le [point de terminaison et la clé d’accès](../How-tos/text-analytics-how-to-access-key.md) générée pendant le processus d’inscription.

## <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python dans votre éditeur ou environnement de développement intégré favori. Ajoutez ensuite les instructions d'importation suivantes à votre fichier.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>S’authentifier avec vos informations d’identification

> [!Tip]
> Pour un déploiement sécurisé des secrets dans des systèmes de production, nous recommandons d’utiliser [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Après avoir établi une variable pour votre clé d’abonnement Analyse de texte, instanciez un objet `CognitiveServicesCredentials` correspondant.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Créer un client Analyse de texte

Créez un objet `TextAnalyticsClient` avec `credentials` et `text_analytics_url` en tant que paramètre. Utilisez la région Azure correcte pour votre abonnement Analyse de texte (par exemple, `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>analyse de sentiments

La charge utile de l’API se compose d’une liste de `documents`, correspondant à des dictionnaires qui contiennent un `id` et un attribut `text`. L’attribut `text` stocke le texte à analyser, et le `id` peut être n'importe quelle valeur. 

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    },
    {
        "id": "2",
        "language": "en",
        "text": "This was a waste of my time. The speaker put me to sleep."
    },
    {
        "id": "3",
        "language": "es",
        "text": "No tengo dinero ni nada que dar..."
    },
    {
        "id": "4",
        "language": "it",
        "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    }
]
```

Appelez la fonction `sentiment()` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le score de sentiment. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Détection de la langue

Créez une liste de dictionnaires, chacun contenant les documents que vous souhaitez analyser. L’attribut `text` stocke le texte à analyser, et le `id` peut être n'importe quelle valeur. 

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    },
    {
        'id': '2',
        'text': 'Este es un document escrito en Español.'
    },
    {
        'id': '3',
        'text': '这是一个用中文写的文件'
    }
]
```

À l’aide du client créé précédemment, appelez `detect_language()` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le premier langage retourné.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconnaissance d’entité

Créez une liste de dictionnaires, contenant les documents que vous souhaitez analyser. L’attribut `text` stocke le texte à analyser, et le `id` peut être n'importe quelle valeur. 


```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es",
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

À l’aide du client créé précédemment, appelez la fonction `entities()` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document, et les entités qu'il contient.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Output

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Créez une liste de dictionnaires, contenant les documents que vous souhaitez analyser. L’attribut `text` stocke le texte à analyser, et le `id` peut être n'importe quelle valeur. 


```python
documents = [
    {
        "id": "1",
        "language": "ja",
        "text": "猫は幸せ"
    },
    {
        "id": "2",
        "language": "de",
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4",
        "language": "es",
        "text": "A mi me encanta el fútbol!"
    }
]
```

À l’aide du client créé précédemment, appelez la fonction `key_phrases()` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document, et les expressions clés qu'il contient.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```

### <a name="output"></a>Output

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que l’API Analyse de texte ?](../overview.md)
* [Exemples de scénarios utilisateur](../text-analytics-user-scenarios.md)
* [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
