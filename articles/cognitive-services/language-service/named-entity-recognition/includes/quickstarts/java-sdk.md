---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.custom: devx-track-java, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 9aa4facab5da6a7b4d8480a6f5afe64ee749d98e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097483"
---
[Documentation de référence](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-stable) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Package](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Kit de développement Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) avec version 8 ou ultérieure
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Créer une ressource Language"  target="_blank">créez une ressource Language </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison.  Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* Pour utiliser la fonctionnalité d’analyse, vous aurez besoin d’une ressource Language avec le niveau tarifaire Standard (S).

## <a name="setting-up"></a>Configuration

### <a name="add-the-client-library"></a>Ajouter la bibliothèque de client

Créez un projet Maven dans l’IDE ou l’environnement de développement de votre choix. Ensuite, ajoutez la dépendance suivante au fichier *pom.xml* de votre projet : Vous trouverez la syntaxe d’implémentation [pour d’autres outils de génération](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) en ligne.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

## <a name="code-example"></a>Exemple de code

Créez un fichier Java nommé `Example.java`. Ouvrez le fichier et copiez le code ci-dessous. N’oubliez pas de remplacer la variable `key` par la clé de votre ressource et la variable `endpoint` par le point de terminaison de votre ressource. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;

public class Example {

    private static String KEY = "replace-with-your-key-here";
    private static String ENDPOINT = "replace-with-your-endpoint-here";

    public static void main(String[] args) {
        TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
        recognizeEntitiesExample(client);
    }
    // Method to authenticate the client object with your key and endpoint
    static TextAnalyticsClient authenticateClient(String key, String endpoint) {
        return new TextAnalyticsClientBuilder()
                .credential(new AzureKeyCredential(key))
                .endpoint(endpoint)
                .buildClient();
    }
    // Example method for recognizing entities in text
    static void recognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The text that need be analyzed.
        String text = "I had a wonderful trip to Seattle last week.";
    
        for (CategorizedEntity entity : client.recognizeEntities(text)) {
            System.out.printf(
                "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
                entity.getText(),
                entity.getCategory(),
                entity.getSubcategory(),
                entity.getConfidenceScore(),
                entity.getOffset(),
                entity.getLength());
        }
    }
}

```

## <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```
