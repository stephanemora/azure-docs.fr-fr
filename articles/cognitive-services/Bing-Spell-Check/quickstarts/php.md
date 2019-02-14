---
title: 'Démarrage rapide : API Vérification orthographique Bing, PHP'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Vérification orthographique Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: abce882edb9b6c97ea3b8a91219f8e2887cfb78d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862307"
---
# <a name="quickstart-for-bing-spell-check-api-with-php"></a>Démarrage rapide pour l’API Vérification orthographique Bing avec PHP 

Cet article vous explique comment utiliser l’[API Vérification orthographique Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) avec PHP. L’API Vérification orthographique renvoie une liste de mots qu’elle ne reconnaît pas avec des suggestions de remplacements. Généralement, vous envoyez du texte à cette API, puis vous effectuez les remplacements suggérés dans le texte ou vous les montrez à l’utilisateur de votre application pour qu’il puisse décider de les effectuer ou non. Cet article explique comment envoyer une requête contenant le texte « Hollo, wrld! » Les remplacements suggérés sont « Hello » et « world ».

## <a name="prerequisites"></a>Prérequis

Vous avez besoin de [PHP 5.6.x](http://php.net/downloads.php) pour exécuter ce code.

Vous devez disposer d’un [compte API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec **API vérification orthographique Bing v7**. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/#lang) est suffisant pour suivre ce guide de démarrage rapide. Vous aurez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou de la clé d’un abonnement payant présente sur votre tableau de bord Azure.  Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="get-spell-check-results"></a>Obtenir les résultats de la vérification orthographique

1. Créez un nouveau projet PHP dans votre environnement IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `subscriptionKey` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// These properties are used for optional headers (see below).
// define("CLIENT_ID", "<Client ID from Previous Response Goes Here>");
// define("CLIENT_IP", "999.999.999.999");
// define("CLIENT_LOCATION", "+90.0000000000000;long: 00.0000000000000;re:100.000000000000");

$host = 'https://api.cognitive.microsoft.com';
$path = '/bing/v7.0/spellcheck?';
$params = 'mkt=en-us&mode=proof';

$input = "Hollo, wrld!";

$data = array (
    'text' => urlencode ($input)
);

// NOTE: Replace this example key with a valid subscription key.
$key = 'ENTER KEY HERE';

// The following headers are optional, but it is recommended
// that they are treated as required. These headers will assist the service
// with returning more accurate results.
//'X-Search-Location' => CLIENT_LOCATION
//'X-MSEdge-ClientID' => CLIENT_ID
//'X-MSEdge-ClientIP' => CLIENT_IP

$headers = "Content-type: application/x-www-form-urlencoded\r\n" .
    "Ocp-Apim-Subscription-Key: $key\r\n";

// NOTE: Use the key 'http' even if you are making an HTTPS request. See:
// http://php.net/manual/en/function.stream-context-create.php
$options = array (
    'http' => array (
        'header' => $headers,
        'method' => 'POST',
        'content' => http_build_query ($data)
    )
);
$context  = stream_context_create ($options);
$result = file_get_contents ($host . $path . $params, false, $context);

if ($result === FALSE) {
    /* Handle error */
}

$json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
echo $json;
?>
```

**Réponse**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel de l’API Vérification orthographique Bing](../tutorials/spellcheck.md)

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de l’API Vérification orthographique Bing](../proof-text.md)
- [Informations de référence sur l’API Vérification orthographique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
