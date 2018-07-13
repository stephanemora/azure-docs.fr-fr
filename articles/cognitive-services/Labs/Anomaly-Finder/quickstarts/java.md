---
title: Guide pratique pour utiliser l’API Recherche d’anomalies avec Java - Microsoft Cognitive Services | Microsoft Docs
description: Obtenez des informations et des exemples de code qui vous aideront à démarrer rapidement avec l’API Détection d’anomalies à l’aide de Java dans Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: 8152c23e6c5332d243d851be56bab1e4085dbe5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369152"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>Utiliser l’API Recherche d’anomalies avec Java

Cet article fournit des informations et des exemples de code qui vont vous aider à démarrer rapidement avec l’API Détection d’anomalies à l’aid de Java en vue d’obtenir les résultats de détection d’anomalies sur des données de série chronologique.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>Obtention de points d’anomalies avec l’API Détection d’anomalies à l’aide de Java

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemple de données de séries chronologiques

L’exemple de points de données de séries chronologiques est le suivant.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>Exemple d’analyse de données et d’obtention des points d’anomalie avec Java

Pour exécuter l’exemple, effectuez les étapes suivantes :
1. Créez une application de ligne de commande.
2. Remplacez la classe Main par le code suivant (conservez les instructions `package`).
3. Remplacez la valeur `[YOUR_SUBSCRIPTION_KEY]` par votre clé d’abonnement valide.
4. Remplacez `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` par l’exemple ou vos propres points de données.
5. Téléchargez ces bibliothèques globales du dépôt Maven vers le répertoire `lib` dans votre projet :
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. Exécutez « Main ».

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Detection: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
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
> [Application Java](../tutorials/java-tutorial.md)
