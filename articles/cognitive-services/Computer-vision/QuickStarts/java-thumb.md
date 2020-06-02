---
title: 'Démarrage rapide : Générer une miniature - REST, Java'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une miniature à partir d’une image par le biais de l’API Vision par ordinateur avec Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: fae54baf3b08ae5e0fa0f640d011b58d686e443e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683154"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-java"></a>Démarrage rapide : Générer une miniature à l’aide de l’API REST Vision par ordinateur et de Java

Dans ce guide de démarrage rapide, vous allez générer une miniature à partir d’une image en utilisant l’API REST de Vision par ordinateur. Vous spécifiez la hauteur et la largeur, qui peuvent différer des proportions de l’image d’entrée. L’API Vision par ordinateur utilise le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage en fonction de cette région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Vous devez utiliser la plateforme [Java&trade;, avec le kit Standard Edition Development 7 ou 8](https://aka.ms/azure-jdks) (JDK 7 ou 8) installé.
- Vous devez disposer d’une clé d’abonnement pour la Vision par ordinateur. Vous pouvez obtenir une clé d’essai gratuit auprès de [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vous pouvez également suivre les instructions mentionnées dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Vision par ordinateur et obtenir votre clé. Ensuite, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la chaîne de point de terminaison de la clé et du service, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>Créer et exécuter l’exemple d’application

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Créez un projet Java dans votre éditeur ou IDE favori. Si l’option est disponible, créez le projet Java à partir d’un modèle d’application de ligne de commande.
1. Importez les bibliothèques suivantes dans votre projet Java. Si vous utilisez Maven, les coordonnées Maven sont fournies pour chaque bibliothèque.
   - [Client HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.X)
   - [Noyau HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.X)
   - [Bibliothèque JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Ajoutez les instructions `import` suivantes à votre classe principale : 

   ```java
    import java.awt.*;
    import javax.swing.*;
    import java.net.URI;
    import java.io.InputStream;
    import javax.imageio.ImageIO;
    import java.awt.image.BufferedImage;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONObject;
   ```

1. Ajoutez le reste de l’exemple de code ci-dessous, sous les instructions import (en indiquant le nom de votre classe, si nécessaire).
1. Ajoutez vos clé d’abonnement et point de terminaison Vision par ordinateur à vos variables d’environnement.
1. Si vous le souhaitez, remplacez la valeur de `imageToAnalyze` par l’URL de votre propre image.
1. Enregistrez les modifications, puis générez le projet Java.
1. Si vous utilisez un IDE, exécutez `GenerateThumbnail`. Dans le cas contraire, procédez à l’exécution à partir de la ligne de commande (commandes ci-dessous).

```java
/**
 * This sample uses the following libraries (create a "lib" folder to place them in): 
 * Apache HTTP client:
 * org.apache.httpcomponents:httpclient:4.5.X 
 * Apache HTTP core:
 * org.apache.httpcomponents:httpccore:4.4.X 
 * JSON library:
 * org.json:json:20180130
 *
 * To build/run from the command line: 
 *     javac GenerateThumbnail.java -cp .;lib\*
 *     java -cp .;lib\* GenerateThumbnail
 */

public class GenerateThumbnail {

    // Add your Computer Vision subscription key and endpoint to your environment
    // variables. Then, close and then re-open your command shell or project for the
    // changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");
    // The endpoint path
    private static final String uriBase = endpoint + "vision/v3.0/generateThumbnail";
    // It's optional if you'd like to use your own image instead of this one.
    private static final String imageToAnalyze = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API method.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            System.out.println("status" + response.getStatusLine().getStatusCode());

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // Displays the given input stream as an image.
    public static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse réussie est retournée sous forme de données binaires qui représentent les données image de la miniature. Si la requête aboutit, la miniature est générée à partir des données binaires de la réponse, et est affichée dans une fenêtre distincte, créée par l’exemple d’application. Si la requête échoue, la réponse s’affiche dans la fenêtre de console. La réponse associée à l’échec de la requête contient un code d’erreur et un message indiquant la cause du problème.

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Java Swing qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créez des miniatures avec un rognage intelligent et enfin détectez, classez, étiquetez et décrivez les caractéristiques visuelles des images.

> [!div class="nextstepaction"]
> [Didacticiel sur l’API Vision par ordinateur avec Java](../Tutorials/java-tutorial.md)

* Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
