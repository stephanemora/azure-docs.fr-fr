---
title: 'Démarrage rapide : Rechercher des mots avec le dictionnaire bilingue, Node.js - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez apprendre à rechercher des traductions alternatives et des exemples d’utilisation pour un texte spécifié à l’aide de Node.js et de l’API REST de traduction de texte Translator Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3fa8d57f3f94a08bc7a7530dde3ae1080f233533
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445015"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-nodejs"></a>Démarrage rapide : Rechercher des mots avec le dictionnaire bilingue en utilisant Node.js

Dans ce démarrage rapide, vous allez apprendre à rechercher des traductions alternatives et des exemples d’utilisation pour un texte spécifié à l’aide de Node.js et de l’API REST de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Node 8.12.x ou version ultérieure](https://nodejs.org/en/)
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet avec votre IDE ou votre éditeur favori, ou créez un dossier sur votre poste de travail. Copiez cet extrait de code dans votre projet/dossier, dans un fichier nommé `alt-translations.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `npm install request uuidv4`.

Ces modules sont requis pour construire la requête HTTP et créer un identificateur unique pour l’en-tête `'X-ClientTraceId'`.

## <a name="set-the-subscription-key"></a>Définir la clé d’abonnement

Ce code tente de lire votre clé d’abonnement Translator Text depuis la variable d’environnement `TRANSLATOR_TEXT_KEY`. Si vous n’êtes pas familiarisé avec les variables d’environnement, vous pouvez définir `subscriptionKey` en tant que chaîne et commentez l’instruction conditionnelle.

Copiez ce code dans votre projet :

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Configurer la requête

La méthode `request()`, accessible via le module de requête, permet de transférer la méthode HTTP, l’URL, les paramètres de requête, les en-têtes et le code JSON en tant qu’objet `options`. Dans cet extrait de code, nous allons configurer la requête :

>[!NOTE]
> Pour plus d’informations sur les points de terminaison, les itinéraires et les paramètres de requête, consultez [API de traduction de texte Translator Text 3.0 : Recherche dans le dictionnaire](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'dictionary/lookup',
    qs: {
      'api-version': '3.0',
      'from': 'en',
      'to': 'es'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Elephants'
    }],
    json: true,
};
```

La manière la plus simple d’authentifier une requête consiste à transmettre votre clé d’abonnement comme `Ocp-Apim-Subscription-Key`en-tête. C’est ce que nous avons fait dans ce modèle. Vous pouvez aussi échanger votre clé d’abonnement pour un jeton d’accès et transmettre le jeton d’accès en tant qu’`Authorization`en-tête pour valider votre requête. 

Si vous utilisez un abonnement multiservice Cognitive Services, vous devez également inclure la `Ocp-Apim-Subscription-Region` dans vos en-têtes de requête. 

Pour en savoir plus, consultez [Authentification](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Effectuer la requête et imprimer la réponse

Ensuite, nous allons créer la requête à l’aide de la méthode `request()`. Cette méthode utilise l’objet `options` que nous avons créé dans la section précédente en tant premier argument, puis imprime la réponse JSON agrémentée.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> Dans cet exemple, nous définissons la requête HTTP dans l’objet `options`. Toutefois, le module de requête prend également en charge des méthodes pratiques, comme `.post` et `.get`. Pour plus d’informations, consultez la section consacrée aux [méthodes pratiques](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Assemblage

Voilà, vous avez installé un programme simple qui appellera l’API de traduction de texte Translator Text et enverra une réponse JSON. Il est maintenant temps d’exécuter votre programme :

```console
node alt-translations.js
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Exemple de réponse

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez codé en dur votre clé d’abonnement dans votre programme, veillez à supprimer la clé d’abonnement une fois ce démarrage rapide terminé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Node.js sur GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Voir aussi

En plus de la détection des langues, découvrez comment utiliser l’API de traduction de texte Translator Text pour :

* [Traduire le texte](quickstart-nodejs-translate.md)
* [Translittérer du texte](quickstart-nodejs-transliterate.md)
* [Identifier la langue par entrée](quickstart-nodejs-detect.md)
* [Obtenir une liste des langues prises en charge](quickstart-nodejs-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-nodejs-sentences.md)
