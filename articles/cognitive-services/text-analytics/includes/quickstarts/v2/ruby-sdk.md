---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/02/2019
ms.author: aahi
ms.openlocfilehash: 637288933037b10aab8c22f7c629e52751057ad6
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281466"
---
[Documentation de référence](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [Package (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Ruby](https://www.ruby-lang.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte 

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Créer une application Ruby

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. Créez ensuite un fichier nommé `GemFile` et un fichier Ruby pour votre code.

```console
mkdir myapp && cd myapp
```

Dans votre `GemFile`, ajoutez les lignes suivantes pour ajouter la bibliothèque cliente en tant que dépendance.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Dans votre fichier Ruby, importez les packages suivants.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. 

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Modèle objet 

Le client Analyse de texte s’authentifie auprès d’Azure à l’aide de votre clé. Le client fournit plusieurs méthodes pour analyser le texte, en tant que chaîne unique ou lot. 

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

Créez une classe nommée `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Dans cette classe, créez une fonction appelée `initialize` pour authentifier le client en utilisant votre clé et de votre point de terminaison. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

En dehors de la classe, utilisez la fonction `new()` du client pour l’instancier.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>analyse de sentiments

Dans l’objet client, créez une fonction appelée `AnalyzeSentiment()` qui prend une liste de documents d’entrée qui seront créés ultérieurement. Appelez la fonction `sentiment()` du client et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le score de sentiment. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

En dehors de la fonction cliente, créez une fonction appelée `SentimentAnalysisExample()` qui sélectionne l’objet `TextAnalyticsClient` créé précédemment. Créez la liste des objets `MultiLanguageInput` contenant les documents à analyser. Chaque objet contient un `id`, `Language` et attribut `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du document et `id` peut être n’importe quelle valeur. Appelez ensuite la fonction `AnalyzeSentiment()` du client.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Appelez la fonction `SentimentAnalysisExample()`.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Détection de la langue

Dans l’objet client, créez une fonction appelée `DetectLanguage()` qui prend une liste de documents d’entrée qui seront créés ultérieurement. Appelez la fonction `detect_language()` du client et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le langage détecté.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

En dehors de la fonction cliente, créez une fonction appelée `DetectLanguageExample()` qui sélectionne l’objet `TextAnalyticsClient` créé précédemment. Créez la liste des objets `LanguageInput` contenant les documents à analyser. Chaque objet contient un `id`, et un attribut `text`. L’attribut `text` stocke le texte à analyser, et le `id` peut être n’importe quelle valeur. Appelez ensuite la fonction `DetectLanguage()` du client.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Appelez la fonction `DetectLanguageExample()`.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Reconnaissance d’entité

Dans l’objet client, créez une fonction appelée `RecognizeEntities()` qui prend une liste de documents d’entrée qui seront créés ultérieurement. Appelez la fonction `entities()` du client et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et les entités reconnues.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

En dehors de la fonction cliente, créez une fonction appelée `RecognizeEntitiesExample()` qui sélectionne l’objet `TextAnalyticsClient` créé précédemment. Créez la liste des objets `MultiLanguageInput` contenant les documents à analyser. Chaque objet contient un `id`, un `language` et un attribut `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du texte et `id` peut être n’importe quelle valeur. Appelez ensuite la fonction `RecognizeEntities()` du client.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Appelez la fonction `RecognizeEntitiesExample()`.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Dans l’objet client, créez une fonction appelée `ExtractKeyPhrases()` qui prend une liste de documents d’entrée qui seront créés ultérieurement. Appelez la fonction `key_phrases()` du client et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et les expressions clés extraites.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

En dehors de la fonction cliente, créez une fonction appelée `KeyPhraseExtractionExample()` qui sélectionne l’objet `TextAnalyticsClient` créé précédemment. Créez la liste des objets `MultiLanguageInput` contenant les documents à analyser. Chaque objet contient un `id`, un `language` et un attribut `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du texte et `id` peut être n’importe quelle valeur. Appelez ensuite la fonction `ExtractKeyPhrases()` du client.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Appelez la fonction `KeyPhraseExtractionExample()`.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
