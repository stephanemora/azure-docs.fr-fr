---
title: 'Démarrage rapide : Créer une requête de recherche visuelle, Java - Recherche visuelle Bing'
titleSuffix: Azure Cognitive Services
description: Montre comment charger une image dans l’API Recherche visuelle Bing pour récupérer des informations sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 56e1b943f03128fa6703a7b15bd0d6ade09089d6
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222622"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-java"></a>Démarrage rapide : Votre première requête Recherche visuelle Bing en Java

L’API Recherche visuelle Bing renvoie des informations sur une image que vous fournissez. Cette image est accessible via son URL, un jeton insights ou par chargement. Pour plus d’informations sur ces options, consultez la section [Qu’est-ce que l’API Recherche visuelle Bing ?](../overview.md) Cet article explique comment charger une image. Il peut se révéler utile de charger une image prise sur un appareil mobile. Par exemple, vous photographiez un monument très connu pour obtenir en retour des informations sur celui-ci. Ces informations sont parfois très variées. 

Si vous chargez une image enregistrée en local, voici les données de formulaire que vous devez inclure dans le corps du POST. Les données du formulaire doivent inclure l’en-tête Content-Disposition. Son paramètre `name` doit être défini sur « image » tandis que le paramètre `filename` peut être défini sur une chaîne quelle qu’elle soit. Le contenu du formulaire correspond au code binaire de l’image. La taille maximale de l’image que vous chargez est de 1 Mo. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Cet article inclut une application console qui envoie une requête d’API Recherche visuelle Bing et affiche les résultats de la recherche au format JSON. Alors que cette application est écrite en Java, l’API est un service web RESTful compatible avec n’importe quel langage de programmation qui peut formuler des requêtes HTTP et analyser JSON. 


## <a name="prerequisites"></a>Prérequis

Pour compiler et exécuter ce code, vous devez disposer de [JDK 7 ou 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Vous pouvez utiliser un IDE Java si vous le souhaitez, mais un éditeur de texte est suffisant.

Pour ce démarrage rapide, vous pouvez utiliser une clé d’abonnement en [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou en version payante.

## <a name="running-the-application"></a>Exécution de l'application

Voici comment charger l’image à l’aide de MultipartEntityBuilder dans Java.

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Téléchargez ou installez la [bibliothèque gson](https://github.com/google/gson). Vous pouvez également l’obtenir via Maven.
2. Créez un projet Java dans votre éditeur ou IDE favori.
3. Ajoutez le code fourni dans un fichier nommé `VisualSearch.java`.
4. Remplacez la valeur `subscriptionKey` par votre clé d’abonnement.
4. Remplacez la valeur `imagePath` par le chemin d’accès de l’image à charger.
5. Exécutez le programme.


```java
package uploadimage;

import java.util.*;
import java.io.*;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class UploadImage2 {

    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere";
    static String imagePath = "<pathtoyourimagetouploadgoeshere>";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addBinaryBody("image", new File(imagePath))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    
}
```

## <a name="next-steps"></a>Étapes suivantes

[Obtenir des informations sur une image à l’aide d’un jeton insights](../use-insights-token.md)  
[Tutoriel de chargement d’images pour Recherche visuelle Bing](../tutorial-visual-search-image-upload.md)
[Tutoriel de l’application à page unique Recherche visuelle Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Présentation de l’API Recherche visuelle Bing](../overview.md)  
[Essayer](https://aka.ms/bingvisualsearchtryforfree)  
[Obtenir une clé d’accès d’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informations de référence sur l’API Recherche visuelle Bing](https://aka.ms/bingvisualsearchreferencedoc)

