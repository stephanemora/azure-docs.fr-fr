---
title: Guide pratique pour utiliser l’API Recherche d’anomalies avec cURL - Microsoft Cognitive Services | Microsoft Docs
description: Obtenez des informations qui vous aideront à démarrer rapidement avec l’API Recherche d’anomalies à l’aide de cURL dans Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3c1d791b8c0478715b4ffa93cd7dfa43f9be4586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369132"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Utiliser l’API Recherche d’anomalies avec cURL

Cet article fournit des informations et des exemples de code qui vont vous aider à démarrer rapidement avec l’API Recherche d’anomalies à l’aide de cURL en vue d’obtenir les résultats d’anomalies de données de série chronologique.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Obtention de points d’anomalies avec l’API Recherche d’anomalies à l’aide de cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemple de données de séries chronologiques

L’exemple de points de données de séries chronologiques est le suivant.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Exemple d’analyse de données et d’obtention des points d’anomalie avec cURL

Les étapes de l’utilisation de l’exemple sont les suivantes.

1. Remplacez la valeur `[YOUR_SUBSCRIPTION_KEY]` par votre clé d’abonnement valide.
2. Remplacez `[YOUR_REGION]` par la région où vous avez obtenu vos clés d’abonnement.
3. Remplacez `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` par l’exemple ou vos propres points de données.
4. Exécutez et vérifiez la réponse.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Exemple de réponse
Une réponse correcte est retournée au format JSON. Voici un exemple de réponse : [!INCLUDE [Response](../includes/response.md)].

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
