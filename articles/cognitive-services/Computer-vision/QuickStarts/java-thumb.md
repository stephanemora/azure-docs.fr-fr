---
title: 'Démarrage rapide : Générer une miniature - REST, Java - Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une miniature à partir d’une image par le biais de l’API Vision par ordinateur avec Java.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 6a169844a0c2c082e0c16f54eab82b1d99f89967
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853062"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-java-in-computer-vision"></a>Démarrage rapide : Générer une miniature avec l’API REST et Java dans la Vision par ordinateur

Dans ce guide de démarrage rapide, vous générez une miniature à partir d’une image par le biais de l’API REST de la Vision par ordinateur. Avec la méthode [Obtenir une miniature](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), vous pouvez générer une miniature d’une image. Vous spécifiez la hauteur et la largeur, qui peuvent différer des proportions de l’image d’entrée. L’API Vision par ordinateur utilise le rognage intelligent pour identifier la région d’intérêt et générer des coordonnées de rognage en fonction de cette région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Vous devez utiliser la plateforme [Java&trade;, avec le kit Standard Edition Development 7 ou 8](https://aka.ms/azure-jdks) (JDK 7 ou 8) installé.
- Vous devez avoir une clé d’abonnement pour Vision par ordinateur. Pour obtenir une clé d’abonnement, consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-application"></a>Créer et exécuter l’exemple d’application

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Créez un projet Java dans votre éditeur ou IDE favori. Si l’option est disponible, créez le projet Java à partir d’un modèle d’application de ligne de commande.
1. Importez les bibliothèques suivantes dans votre projet Java. Si vous utilisez Maven, les coordonnées Maven sont fournies pour chaque bibliothèque.
   - [Client HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Noyau HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Bibliothèque JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Ajoutez les instructions `import` suivantes au fichier qui contient la classe publique `Main` pour votre projet.  

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

1. Remplacez la classe publique `Main` par le code suivant et, au besoin, effectuez les modifications suivantes dans le code :
   1. Remplacez la valeur de `subscriptionKey` par votre clé d’abonnement.
   1. Si nécessaire, remplacez la valeur de `uriBase` par l’URL du point de terminaison pour la méthode [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) à partir de la région Azure dans laquelle vous avez obtenu vos clés d’abonnement.
   1. Le cas échéant, remplacez la valeur de `imageToAnalyze` par l’URL d’une image différente pour laquelle vous souhaitez générer une miniature.
1. Enregistrez, puis créez le projet Java.
1. Si vous utilisez un IDE, exécutez `Main`. Sinon, ouvrez une fenêtre d’invite de commandes et utilisez la commande `java` pour exécuter la classe compilée. Par exemple : `java Main`.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).


public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

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
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

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
        }
    }

    // Displays the given input stream as an image.
    private static void displayImage(InputStream inputStream) {
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

Une réponse correcte est retournée sous forme de données binaires qui représentent les données d’image de la miniature. Si la requête aboutit, la miniature est générée à partir des données binaires de la réponse, et est affichée dans une fenêtre distincte, créée par l’exemple d’application. Si la requête échoue, la réponse s’affiche dans la fenêtre de la console. La réponse associée à l’échec de la requête contient un code d’erreur et un message indiquant la cause du problème.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez le projet Java, y compris la classe compilée et les bibliothèques importées.

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Java Swing qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent, ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Didacticiel sur l’API Vision par ordinateur avec Java](../Tutorials/java-tutorial.md)
