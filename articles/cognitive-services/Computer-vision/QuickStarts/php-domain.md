---
title: 'Démarrage rapide : Contenu d’image spécifique à un domaine - REST, PHP'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous utilisez un modèle de domaine pour identifier des monuments dans une image à l’aide de l’API Vision par ordinateur avec PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6e0705841b9dca08bab1526e0df34c819406b91d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858142"
---
# <a name="quickstart-recognize-domain-specific-content-using-the-rest-api-and-php-with-computer-vision"></a>Démarrage rapide : Reconnaître un contenu spécifique à un domaine à l’aide de l’API REST et de PHP avec Vision par ordinateur

Dans ce guide de démarrage rapide, vous utilisez un modèle de domaine pour identifier des monuments, ou éventuellement des célébrités, dans une image stockée à distance à l’aide de l’API REST de Vision par ordinateur. Avec la méthode [Reconnaître le contenu de domaine spécifique](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), vous appliquez un modèle de domaine spécifique pour reconnaître le contenu dans une image.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- [PHP](https://secure.php.net/downloads.php) doit être installé.
- [Pear](https://pear.php.net) doit être installé.
- Vous devez avoir une clé d’abonnement pour Vision par ordinateur. Pour obtenir une clé d’abonnement, consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Créer et exécuter l’exemple

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Installez le package [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) PHP5.
   1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
   1. Exécutez la commande suivante :

      ```console
      pear install HTTP_Request2
      ```

   1. Une fois que vous avez installé le package, fermez la fenêtre d’invite de commandes.

1. Copiez le code ci-après dans un éditeur de texte.
1. Modifiez le code comme ci-dessous :
    1. Remplacez la valeur de `subscriptionKey` par votre clé d’abonnement.
    1. Si nécessaire, remplacez la valeur de `uriBase` par l’URL du point de terminaison de la méthode [Reconnaître le contenu de domaine spécifique](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) dans la région Azure où vous avez obtenu vos clés d’abonnement.
    1. Remplacez éventuellement la valeur de `imageUrl` par l’URL d’une autre image que vous voulez analyser.
    1. Si vous le souhaitez, remplacez la valeur du paramètre de requête `domain` par `celebrites` pour utiliser le modèle de domaine `celebrities` au lieu du modèle de domaine `landmarks`.
1. Enregistrez le code dans un fichier avec une extension `.php`. Par exemple : `use-domain-model.php`.
1. Ouvrez une fenêtre de navigateur avec prise en charge de PHP.
1. Faites glisser et déposez le fichier dans la fenêtre du navigateur.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple de site web analyse et affiche une réponse réussie dans la fenêtre du navigateur, comme dans l’exemple suivant :

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez le fichier, puis désinstallez le package `HTTP_Request2` PHP5. Pour désinstaller le package, effectuez les étapes suivantes :

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Exécutez la commande suivante :

   ```console
   pear uninstall HTTP_Request2
   ```

3. Une fois que vous avez désinstallé le package, fermez la fenêtre d’invite de commandes.

## <a name="next-steps"></a>Étapes suivantes

Explorez l’API Vision par ordinateur utilisée pour analyser une image, détecter des célébrités et des monuments, créer une miniature et extraire le texte imprimé ou manuscrit. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorer l’API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
