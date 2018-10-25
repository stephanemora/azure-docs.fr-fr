---
title: Guide pratique pour utiliser l’API Recherche d’anomalies avec C# - Microsoft Cognitive Services | Microsoft Docs
description: Obtenez des informations et des exemples de code qui vous aideront à démarrer rapidement avec l’API Recherche d’anomalies à l’aide de C# dans Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 26653d03b8d96cca0674685d2b20ac1d1a35ea85
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457865"
---
# <a name="use-the-anomaly-finder-api-with-c"></a>Utiliser l’API Recherche d’anomalies avec C#

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Cet article fournit des informations et des exemples de code qui vont vous aider à démarrer rapidement avec l’API Recherche d’anomalies à l’aide de C# en vue d’obtenir les résultats d’anomalies de données de série chronologique.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-c"></a>Obtention de points d’anomalies avec l’API Recherche d’anomalies à l’aide de C#

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data-points"></a>Exemple de points de données de séries chronologiques

L’exemple de points de données de séries chronologiques est le suivant.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-c-example"></a>Exemple d’analyse de données et d’obtention des points d’anomalie avec C#

Les étapes de l’utilisation de l’exemple sont les suivantes.

1. Créez une solution Console dans Visual Studio.
2. Remplacez le fichier Program.cs par le code suivant et ajoutez la référence à System.Net.Http.
3. Remplacez la valeur `[YOUR_SUBSCRIPTION_KEY]` par votre clé d’abonnement valide.
4. Remplacez `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` par vos points de données.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading.Tasks;

namespace Console
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

        const string endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

        // Replace the request data with your real data.
        const string requestData = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";
        static void Main(string[] args)
        {
            var match = Regex.Match(endpoint, "(?<BaseAddress>https://[^/]+)(?<Url>/*.+)");
            if (match.Success)
            {
                var res = Request(
                    match.Groups["BaseAddress"].Value,
                    match.Groups["Url"].Value,
                    subscriptionKey,
                    requestData).Result;
                System.Console.Write(res);
            }
            else
            {
                System.Console.Write("Incorrect endpoint.");
            }
        }

        /// <summary>
        /// Call API to detect the anomaly points
        /// </summary>
        /// <param name="baseAddress">Base address of the API endpoint.</param>
        /// <param name="endpoint">The endpoint of the API</param>
        /// <param name="subscriptionKey">The subscription key applied  </param>
        /// <param name="requestData">The JSON string for requet data points</param>
        /// <returns>The JSON string for anomaly points and expected values.</returns>
        static async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
        {
            using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
            {
                client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

                var content = new StringContent(requestData, Encoding.UTF8, "application/json");
                var res = await client.PostAsync(endpoint, content);
                if (res.IsSuccessStatusCode)
                {
                    return await res.Content.ReadAsStringAsync();
                }
                else
                {
                    return $"ErrorCode: {res.StatusCode}";
                }
            }
        }
    }
}

```

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Voici un exemple de réponse.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application C#](../tutorials/csharp-tutorial.md)
