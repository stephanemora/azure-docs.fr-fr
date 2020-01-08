---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: eaaa6ea7c5986a71f298119b5074ae32504ab73b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446227"
---
<a name="HOLTop"></a>

[Documentation de référence](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Package (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [Exemples](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org/).

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```

Créez un fichier nommé `index.js` et ajoutez les bibliothèques suivantes :

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Créez des variables pour le point de terminaison et la clé d’abonnement Azure de votre ressource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const subscription_key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `@azure/ms-rest-js` et `@azure/cognitiveservices-textanalytics` :

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client fournit plusieurs méthodes pour analyser le texte, en tant que chaîne unique ou lot.

Le texte est envoyé à l’API sous la forme d’une liste de `documents`, qui sont des objets `dictionary` contenant une combinaison d’attributs `id`, `text` et `language` en fonction de la méthode utilisée. L’attribut `text` stocke le texte à analyser dans le `language` d’origine, et vous pouvez utiliser n’importe quelle valeur pour `id`. 

L’objet Response est une liste contenant les informations d’analyse de chaque document. 

## <a name="code-examples"></a>Exemples de code

* [Authentifier le client](#authenticate-the-client)
* [Analyse des sentiments](#sentiment-analysis)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entités](#entity-recognition)
* [Extraction de phrases clés](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Authentifier le client

Créez un objet [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) avec `credentials` et `endpoint`en tant que paramètre.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>analyse de sentiments

Créez une liste d’objets de dictionnaire, contenant les documents que vous souhaitez analyser. Appelez la méthode [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) du client et récupérez le [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) qu’elle renvoie. Effectuez une itération dans la liste des résultats, puis imprimez l’ID de chaque document et le score de sentiment. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

## <a name="language-detection"></a>Détection de la langue

Générez une liste d’objets de dictionnaire contenant vos documents. Appelez la méthode [detectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) du client et récupérez le [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)renvoyé. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le langage.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconnaissance d’entité

Générez une liste d’objets contenant vos documents. Appelez la méthode [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) du client et récupérez l’objet [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Effectuez une itération dans la liste des résultats, puis imprimez l’ID de chaque document. Pour chaque détectée entité, imprimez son nom Wikipédia, le type et les sous-types (le cas échéant), ainsi que les emplacements dans le texte d’origine.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
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

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Générez une liste d’objets contenant vos documents. Appelez la méthode [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) du client et récupérez l’objet [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult). Effectuez une itération dans les résultats, puis imprimez l’ID de chaque document et les phases clés détectées.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```
