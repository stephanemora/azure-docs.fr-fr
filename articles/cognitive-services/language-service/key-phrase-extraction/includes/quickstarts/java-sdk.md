---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.custom: devx-track-java, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 7f28d065637d2d2200fef045515e88b9bec03570
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097004"
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
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;


public class Example {

    private static String KEY = "replace-with-your-key-here";
    private static String ENDPOINT = "replace-with-your-endpoint-here";

    public static void main(String[] args) {
        TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
        extractKeyPhrasesExample(client);
    }
    // Method to authenticate the client object with your key and endpoint
    static TextAnalyticsClient authenticateClient(String key, String endpoint) {
        return new TextAnalyticsClientBuilder()
                .credential(new AzureKeyCredential(key))
                .endpoint(endpoint)
                .buildClient();
    }
    // Example method for extracting key phrases from text
    static void extractKeyPhrasesExample(TextAnalyticsClient client)
    {
        // The text to be analyzed
        String text = "My cat might need to see a veterinarian.";

        System.out.printf("Recognized phrases: %n");
        for (String keyPhrase : client.extractKeyPhrases(text)) {
            System.out.printf("%s%n", keyPhrase);
        }
    }
}

```

## <a name="output"></a>Output

```console
Recognized phrases: 

cat
veterinarian
```
