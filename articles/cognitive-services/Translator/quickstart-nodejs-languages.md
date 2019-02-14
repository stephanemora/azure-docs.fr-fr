---
title: 'Démarrage rapide : Obtenir une liste des langues prises en charge, Node.js - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez obtenir la liste des langues prises en charge pour la traduction, la translittération et la recherche dans le dictionnaire, ainsi que des exemples d’utilisation de l’API de traduction de texte Translator Text avec Node.js.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: erhopf
ms.openlocfilehash: eb852f0449a7f59ba0235ffc8ad7c8aff6f3babc
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892747"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-with-nodejs"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text et Node.js pour obtenir la liste des langues prises en charge

Dans ce démarrage rapide, vous allez apprendre à effectuer une requête GET qui retourne la liste des langues prises en charge à l’aide de Node.js et de l’API REST Traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Node 8.12.x ou version ultérieure](https://nodejs.org/en/)

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un nouveau projet dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `get-languages.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `npm install request uuidv4`.

Ces modules sont requis pour construire la requête HTTP et créer un identificateur unique pour l’en-tête `'X-ClientTraceId'`.

## <a name="configure-the-request"></a>Configurer la requête

La méthode `request()`, accessible via le module de requête, permet de transférer la méthode HTTP, l’URL, les paramètres de requête, les en-têtes et le code JSON en tant qu’objet `options`. Dans cet extrait de code, nous allons configurer la requête :

>[!NOTE]
> Pour plus d’informations sur les points de terminaison, les itinéraires et les paramètres de requête, consultez [API de traduction de texte Translator Text 3.0 : Langues](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```javascript
let options = {
    method: 'GET',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

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
node get-languages.js
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Exemple de réponse

Cet exemple a été tronqué pour afficher un extrait du résultat :

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [Obtenir des traductions alternatives](quickstart-nodejs-dictionary.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-nodejs-sentences.md)
