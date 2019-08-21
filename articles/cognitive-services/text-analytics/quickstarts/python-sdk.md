---
title: 'Démarrage rapide : Bibliothèque de client Analyse de texte pour Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Suivez ce démarrage rapide pour utiliser l’API Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: aahi
ms.openlocfilehash: 1d7ad19a58327ba508ccb4e47d12d3d0f50465f4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883999"
---
# <a name="quickstart-text-analytics-client-library-for-python"></a>Démarrage rapide : Bibliothèque de client Analyse de texte pour Python
<a name="HOLTop"></a>

Découvrez comment utiliser la bibliothèque de client Analyse de texte pour Python. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. 

Utilisez la bibliothèque de client Analyse de texte pour Python dans le cadre des opérations suivantes :

* analyse de sentiments
* Détection de la langue
* Reconnaissance d’entité
* Extraction d’expressions clés


[Documentation de référence](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Package (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [Exemples C#](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Analyse de texte en utilisant le [portail Azure](../../cognitive-services-apis-create-account.md) ou [Azure CLI](../../cognitive-services-apis-create-account-cli.md) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services/#decision) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle sera disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’évaluation, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python dans votre éditeur ou IDE favori. Importez ensuite les bibliothèques suivantes.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```python
# replace this endpoint with the correct one for your Azure resource. 
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
# This sample assumes you have created an environment variable for your key
key = os.environ["TEXT_ANALYTICS_SUBSCRIPTION_KEY"]
credentials = CognitiveServicesCredentials(key)
```

## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client fournit plusieurs méthodes pour analyser le texte, en tant que chaîne unique ou lot. 

Le texte est envoyé à l’API sous la forme d’une liste de `documents`, qui sont des objets `dictionary` contenant une combinaison d’attributs `id`, `text` et `language` en fonction de la méthode utilisée. L’attribut `text` stocke le texte à analyser dans le `language` d’origine, et vous pouvez utiliser n’importe quelle valeur pour `id`. 

L’objet Response est une liste contenant les informations d’analyse de chaque document. 

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client Analyse de texte pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Analyse des sentiments](#sentiment-analysis)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entités](#entity-recognition)
* [Extraction de phrases clés](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Authentifier le client

Créez un objet [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) avec `credentials` et `text_analytics_url`en tant que paramètre. Utilisez la région Azure correcte pour votre abonnement Analyse de texte (par exemple, `westcentralus`).

```python
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>analyse de sentiments

Appelez la fonction [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le score de sentiment. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    }
]
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
```

## <a name="language-detection"></a>Détection de la langue

À l’aide du client créé précédemment, appelez [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le premier langage retourné.

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    }
]
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
```

## <a name="entity-recognition"></a>Reconnaissance d’entité

À l’aide du client créé précédemment, appelez la fonction [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document, et les entités qu'il contient.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    }
]
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
```

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

À l’aide du client créé précédemment, appelez la fonction [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document, et les expressions clés qu'il contient.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    }
]
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```


### <a name="output"></a>Output

```console
Document Id:  3
    Phrases:
         cat
         veterinarian
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources liées au groupe de ressources.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconnaissance d’entités](../how-tos/text-analytics-how-to-entity-linking.md)
* [Détecter la langue](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconnaissance de la langue](../how-tos/text-analytics-how-to-language-detection.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).
