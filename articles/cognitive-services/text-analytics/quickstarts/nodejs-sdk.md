---
title: 'Démarrage rapide : Utilisation de Node.js pour appeler l’API Analyse de texte'
titleSuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Analyse de texte.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697479"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Démarrage rapide : Utilisation de Node.js pour appeler le service cognitif Analyse de texte
<a name="HOLTop"></a>

Utilisez ce guide de démarrage rapide pour commencer l’analyse du langage avec le SDK Analyse de texte pour Node.js. Si l’API REST [Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759711) est compatible avec la plupart des langages de programmation, le kit SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Consultez les [définitions des API](//go.microsoft.com/fwlink/?LinkID=759346) pour accéder à la documentation technique des API.

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/)
* Kit de développement logiciel (SDK) Node.js [Analyse de texte](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) Vous pouvez installer le SDK avec :

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Vous devez également avoir le [point de terminaison et la clé d’accès](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) générée pendant le processus d’inscription.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Créer une application Node.js et installer le kit de développement

Après avoir installé Node.js, créez un projet de nœud. Créez un nouveau répertoire pour votre application et accédez à son répertoire.

```mkdir myapp && cd myapp```

Exécutez ```npm init``` pour créer une application de nœud avec un fichier package.json. Installez les packages NPM `ms-rest-azure` et `azure-cognitiveservices-textanalytics` :

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Le fichier de package.json de votre application sera mis à jour avec les dépendances.

## <a name="authenticate-your-credentials"></a>S’authentifier avec vos informations d’identification

Créer un nouveau fichier `index.js` dans la racine du projet et importez les bibliothèques installées

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Créez une variable pour votre clé d’abonnement Analyse de texte.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Pour un déploiement sécurisé des secrets dans des systèmes de production, nous recommandons d’utiliser [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Créer un client Analyse de texte

Créez un objet `TextAnalyticsClient` avec `credentials` en tant que paramètre. Utilisez la région Azure correcte pour votre abonnement Analyse de texte.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>analyse de sentiments

Créez la liste des objets contenant les documents à analyser. La charge utile de l’API se compose d’une liste de `documents`, contenant les attributs `id`, `language` et `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du document et `id` peut être n’importe quelle valeur. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
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
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Détection de la langue

Générez une liste d’objets contenant vos documents. La charge utile de l’API se compose d’une liste de `documents`, contenant les attributs `id` et `text`. L’attribut `text` stocke le texte à analyser, et le `id` peut être n’importe quelle valeur.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
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
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconnaissance d’entité

Générez une liste d’objets contenant vos documents. La charge utile de l’API se compose d’une liste de `documents`, contenant les attributs `id`, `language` et `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du document et `id` peut être n’importe quelle valeur.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

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
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Générez une liste d’objets contenant vos documents. La charge utile de l’API se compose d’une liste de `documents`, contenant les attributs `id`, `language` et `text`. L’attribut `text` stocke le texte à analyser, `language` correspond au langage du document et `id` peut être n’importe quelle valeur.

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
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
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi

 [Vue d’ensemble Analyse de texte](../overview.md) [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
