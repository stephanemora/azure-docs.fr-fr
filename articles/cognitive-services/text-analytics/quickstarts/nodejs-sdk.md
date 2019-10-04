---
title: 'Démarrage rapide : Bibliothèque de client Analyse de texte pour Node.js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Analyse de texte.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: shthowse
ms.openlocfilehash: b5eb327daa74d8e6f384d6f8e1054fb265ce4a7d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375865"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>Démarrage rapide : Bibliothèque de client Analyse de texte pour Node.js
<a name="HOLTop"></a>

Découvrez comment utiliser la bibliothèque de client Analyse de texte pour Node.js. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. 

Utilisez la bibliothèque de client Analyse de texte pour Node.js dans le cadre des opérations suivantes :

* analyse de sentiments
* Détection de la langue
* Reconnaissance d’entité
* Extraction d’expressions clés

[Documentation de référence](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Package (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [Exemples](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org/).

## <a name="setting-up"></a>Configuration

### <a name="create-a-text-analytics-azure-resource"></a>Créer une ressource Azure pour Analyse de texte

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Analyse de texte en utilisant le [Portail Azure](../../cognitive-services-apis-create-account.md) ou [Azure CLI](../../cognitive-services-apis-create-account-cli.md) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services/#decision) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle sera disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’évaluation, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `TEXTANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```

Créez un fichier nommé `index.js` et importez les bibliothèques suivantes :

```javascript
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```

Créez des variables pour le point de terminaison et la clé d’abonnement Azure de votre ressource. Obtenez ces valeurs à partir des variables d’environnement TEXT_ANALYTICS_SUBSCRIPTION_KEY et TEXT_ANALYTICS_ENDPOINT. Si vous avez créé ces variables d’environnement après avoir commencé à modifier l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou l’interpréteur de commandes que vous utilisez pour accéder aux variables.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `@azure/ms-rest-js` et `@azure/cognitiveservices-textanalytics` :

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

## <a name="object-model"></a>Modèle objet

Le client Analyse de texte est un objet [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client fournit plusieurs méthodes pour analyser le texte, en tant que chaîne unique ou lot.

Le texte est envoyé à l’API sous la forme d’une liste de `documents`, qui sont des objets `dictionary` contenant une combinaison d’attributs `id`, `text` et `language` en fonction de la méthode utilisée. L’attribut `text` stocke le texte à analyser dans le `language` d’origine, et vous pouvez utiliser n’importe quelle valeur pour `id`. 

L’objet Response est une liste contenant les informations d’analyse de chaque document. 

## <a name="code-examples"></a>Exemples de code

* [Authentifier le client](#authenticate-the-client)
* [Analyse des sentiments](#sentiment-analysis)
* [Détection de la langue](#language-detection)
* [Reconnaissance d’entités](#entity-recognition)
* [Extraction de phrases clés](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Authentifier le client

Créez un objet [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) avec `credentials` et `endpoint`en tant que paramètre.

```javascript
const creds = new CognitiveServicesCredentials.ApiKeyCredentials({ inHeader: { 'Ocp-Apim-Subscription-Key': subscription_key } });
const client = new TextAnalyticsAPIClient.TextAnalyticsClient(creds, endpoint);
```

## <a name="sentiment-analysis"></a>analyse de sentiments

Créez la liste des objets contenant les documents à analyser.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
]}
```

Appelez `client.sentiment` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le score de sentiment. Un score proche de 0 indique un sentiment négatif, tandis qu’un score proche de 1 dénote un sentiment positif.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>Détection de la langue

Générez une liste d’objets contenant vos documents.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
    ]
    };
```

Appelez `client.detectLanguage()` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et le premier langage retourné.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
```

## <a name="entity-recognition"></a>Reconnaissance d’entité

Générez une liste d’objets contenant vos documents.

```javascript
const inputDocuments = {
    documents: [
        { language: "en", id: "1", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800" }
    ]
}
```

Appelez `client.entities()` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document. Pour chaque détectée entité, imprimez son nom Wikipédia, le type et les sous-types (le cas échéant), ainsi que les emplacements dans le texte d’origine.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
```

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Générez une liste d’objets contenant vos documents.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
    ]
    };
```

Appelez `client.keyPhrases()` et obtenez le résultat. Effectuez ensuite une itération dans les résultats, puis imprimez l’ID de chaque document et les phases clés détectées.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Exécutez votre code avec `node index.js` dans la fenêtre de votre console.

### <a name="output"></a>Output

```console
[
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

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
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).
