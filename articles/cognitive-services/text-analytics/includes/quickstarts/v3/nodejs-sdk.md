---
title: 'Démarrage rapide : Bibliothèque cliente Analyse de texte v3 pour Node.js | Microsoft Docs'
description: Découvrez comment utiliser la bibliothèque cliente Analyse de texte v3 pour Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 69899f521e73cb5af1af145a0915dbe1a017f307
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281148"
---
<a name="HOLTop"></a>

[Documentation de référence](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Package (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)

> [!NOTE]
> * Ce guide de démarrage rapide utilise la version `3.0-preview` de la bibliothèque cliente Analyse de texte, qui comprend une préversion publique des fonctionnalités [Analyse des sentiments](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) et [Reconnaissance d’entité nommée (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) améliorées.
> * Le code indiqué dans cet article utilise un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Pour les scénarios de production, nous vous recommandons d’envoyer les chaînes dans des lots afin d’optimiser les performances et la scalabilité. Par exemple, l’appel de `SentimentBatchAsync()` au lieu de `Sentiment()`.

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org/).

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `@azure/cognitiveservices-textanalytics` :

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

Créez un fichier nommé `index.js` et ajoutez les bibliothèques suivantes :

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

Créez des variables pour le point de terminaison et la clé Azure de votre ressource.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet `TextAnalyticsClient` qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client fournit plusieurs méthodes pour analyser le texte, en tant que chaîne unique ou lot.

Le texte est envoyé à l’API sous la forme d’une liste de `documents`, qui sont des objets `dictionary` contenant une combinaison d’attributs `id`, `text` et `language` en fonction de la méthode utilisée. L’attribut `text` stocke le texte à analyser dans le `language` d’origine, et vous pouvez utiliser n’importe quelle valeur pour `id`. 

L’objet Response est une liste contenant les informations d’analyse de chaque document. 

## <a name="code-examples"></a>Exemples de code

* [Authentification du client](#client-authentication)
* [Analyse des sentiments](#sentiment-analysis) (préversion publique)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entité nommée](#named-entity-recognition-public-preview) (préversion publique)
* [Reconnaissance d’entité nommée : informations personnelles](#named-entity-recognition---personal-information-public-preview) (préversion publique)
* [Liaison d’entités](#entity-linking)
* [Extraction de phrases clés](#key-phrase-extraction)

## <a name="client-authentication"></a>Authentification du client

Créez un objet `TextAnalyticsClient` avec vos clé et point de terminaison comme paramètres.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>Analyse des sentiments (préversion publique)

> [!NOTE]
> Le code ci-dessous est destiné à Analyse des sentiments v3, disponible en préversion publique.

Créez un tableau de chaînes contenant le document que vous souhaitez analyser. Appelez la méthode `analyzeSentiment()` du client et récupérez l’objet `SentimentBatchResult` retourné. Effectuez une itération dans la liste des résultats, puis affichez l’ID de chaque document et le sentiment au niveau du document avec les scores de confiance. Pour chaque document, le résultat contient le sentiment au niveau des phrases ainsi que les décalages, la longueur et les scores de confiance.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

## <a name="language-detection"></a>Détection de la langue

Créez un tableau de chaînes contenant le document que vous souhaitez analyser. Appelez la méthode `detectLanguages()` du client et récupérez l’objet `DetectLanguageResult` retourné. Effectuez ensuite une itération dans les résultats, puis affichez l’ID de chaque document avec les langues principale et détectée respectives.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>Reconnaissance d’entité nommée

> [!NOTE]
> Le code ci-dessous concerne Reconnaissance d’entité nommée v3, disponible en préversion publique.

Créez un tableau de chaînes contenant le document que vous souhaitez analyser. Appelez la méthode `recognizeEntities()` du client et récupérez l’objet `RecognizeEntitiesResult`. Effectuez une itération dans la liste des résultats et affichez le nom de l’entité, le type, le sous-type, le décalage, la longueur et le score.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];

    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Reconnaissance d’entité nommée : informations personnelles (préversion publique)

> [!NOTE]
> Le code ci-dessous concerne la détection d’informations personnelles à l’aide de Reconnaissance d’entité nommée v3, disponible en préversion publique.

Créez un tableau de chaînes contenant le document que vous souhaitez analyser. Appelez la méthode `recognizePiiEntities()` du client et récupérez l’objet `EntitiesBatchResult`. Effectuez une itération dans la liste des résultats et affichez le nom de l’entité, le type, le sous-type, le décalage, la longueur et le score.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Liaison d’entités

Créez un tableau de chaînes contenant le document que vous souhaitez analyser. Appelez la méthode `recognizeLinkedEntities()` du client et récupérez l’objet `RecognizeLinkedEntitiesResult`. Effectuez une itération dans la liste des résultats et affichez le nom de l’entité, l’ID, la source de données, l’URL et les correspondances. Chaque objet dans le tableau `matches` contient le décalage, la longueur et le score pour la correspondance concernée.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Créez un tableau de chaînes contenant le document que vous souhaitez analyser. Appelez la méthode `extractKeyPhrases()` du client et récupérez l’objet `ExtractKeyPhrasesResult` retourné. Effectuez une itération dans les résultats, puis imprimez l’ID de chaque document et les phases clés détectées.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```
