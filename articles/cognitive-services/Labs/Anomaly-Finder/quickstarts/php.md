---
title: Guide pratique pour utiliser l’API Recherche d’anomalies avec PHP - Microsoft Cognitive Services | Microsoft Docs
description: Obtenez des informations et des exemples de code qui vous aideront à démarrer rapidement avec la Recherche d’anomalies à l’aide de PHP dans Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: cb149d7a0a1eef2c096a3562f5d0256deca19527
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458069"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Utiliser l’API Recherche d’anomalies avec PHP

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Cet article fournit des informations et des exemples de code qui vont vous aider à démarrer rapidement avec l’API Recherche d’anomalies à l’aide de PHP en vue d’obtenir les résultats d’anomalies de données de série chronologique.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Obtention de points d’anomalies avec l’API Recherche d’anomalies à l’aide de PHP
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemple de données de séries chronologiques
L’exemple de données de séries chronologiques est le suivant.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Exemple d’analyse de données et d’obtention des points d’anomalie avec PHP
1. Remplacez la valeur `[YOUR_SUBSCRIPTION_KEY]` par votre clé d’abonnement valide.
2. Remplacez `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` par l’exemple ou vos propres points de données.
3. Exécutez et vérifiez la réponse.

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}
?>
```

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Voici un exemple de réponse.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
