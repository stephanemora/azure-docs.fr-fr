---
title: 'Tutoriel : Effectuer des opérations sur les images - Java'
titlesuffix: Azure Cognitive Services
description: Explorez une application Java Swing de base qui utilise l’API Vision par ordinateur dans Azure Cognitive Services. Effectuez une reconnaissance OCR, créez des miniatures et utilisez des fonctionnalités visuelles dans une image.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.custom: seodec18
ms.date: 09/21/2017
ms.openlocfilehash: 4f6af31ba6b04ddbecb7cb42cebe345b6af720ac
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868087"
---
# <a name="tutorial-computer-vision-api-java"></a>Didacticiel : API Vision par ordinateur avec Java

Ce tutoriel montre les fonctionnalités de l’API REST Vision par ordinateur dans Azure Cognitive Services.

Explorez une application Java Swing qui utilise l’API REST Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent ainsi que détecter, catégoriser, étiqueter et décrire des caractéristiques visuelles, dont les visages, dans une image. Cet exemple vous permet d’envoyer une URL d’image à des fins d’analyse ou de traitement. Vous pouvez utiliser cet exemple open source comme modèle pour générer votre propre application en Java qui utilisera l’API REST Vision par ordinateur.

Ce tutoriel montre comment utiliser Vision par ordinateur pour :

> [!div class="checklist"]
> * Analyser une image
> * Identifier un élément géographique naturel ou artificiel dans une image
> * Identifier une célébrité dans une image
> * Créer une miniature de qualité à partir d’une image
> * Lire du texte imprimé dans une image
> * Lire du texte manuscrit dans une image

L’application de formulaire Java Swing a déjà été écrite, mais elle n’a pas de fonctionnalités. Dans ce tutoriel, vous ajoutez le code spécifique à l’API REST Vision par ordinateur pour compléter la fonctionnalité de l’application.

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Plateforme requise

Ce tutoriel a été développé à l’aide de l’IDE NetBeans, plus précisément, la version **Java SE** de NetBeans, que vous pouvez [télécharger ici](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>S’abonner à l’API Vision par ordinateur et obtenir une clé d’abonnement 

Avant de créer l’exemple, vous devez vous abonner à l’API Vision par ordinateur qui fait partie d’Azure Cognitive Services. Pour plus d’informations sur l’abonnement et la gestion des clés, consultez [Abonnements](https://azure.microsoft.com/try/cognitive-services/). Les clés primaire et secondaire peuvent toutes deux être utilisées dans ce tutoriel. 

## <a name="acquire-the-incomplete-tutorial-project"></a>Acquérir le projet du didacticiel incomplet

### <a name="download-the-tutorial-project"></a>Télécharger le projet du tutoriel

1. Accédez au dépôt [Tutoriel sur l’API Vision par ordinateur avec Java dans Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Cliquez sur le bouton **Clone ou Download** (Cloner ou télécharger).
1. Cliquez sur **Download ZIP** (Télécharger un ZIP) pour télécharger un fichier .zip du projet du tutoriel.

Il est inutile d’extraire le contenu du fichier .zip, car NetBeans importe le projet directement à partir de ce fichier.

### <a name="import-the-tutorial-project"></a>Importer le projet du tutoriel

Importez le fichier **cognitive-services-java-computer-vision-tutorial-master.zip** dans NetBeans.

1. Dans NetBeans, cliquez sur **File** > **Import Project** > **From ZIP...** (Fichier/Importer des projets/À partir d’un ZIP...). La boîte de dialogue **Import Project(s) from ZIP** (Importer des projets à partir de ZIP) s’affiche.
1. Dans le champ **ZIP File:** (Fichier ZIP), cliquez sur le bouton **Browse** (Parcourir) pour rechercher le fichier **cognitive-services-java-computer-vision-tutorial-master.zip**, puis cliquez sur **Open** (Ouvrir).
1. Cliquez sur **Import** (Importer) dans la boîte de dialogue **Import Project(s) from ZIP**.
1. Dans le panneau **Projects** (Projets), développez **ComputerVision** > **Source Packages** > **&lt;default package&gt;** (ComputerVision/Packages sources/package par défaut). 
   Certaines versions de NetBeans affichent **src** à la place de **Source Packages** > **&lt;default package&gt;**. Dans ce cas, développez **src**.
1. Double-cliquez sur **MainFrame.java** pour charger le fichier dans l’éditeur NetBeans. L’onglet **Design** (Conception) du fichier **MainFrame.java** s’affiche.
1. Cliquez sur l’onglet **Source** pour afficher le code source Java.

### <a name="build-and-run-the-tutorial-project"></a>Générer et exécuter le projet du tutoriel

1. Appuyez sur **F6** pour générer et exécuter l’application du tutoriel.

    Dans l’application du tutoriel, cliquez sur un onglet pour afficher le volet de cette fonctionnalité. Les boutons étant associés à des méthodes vides, ils n’effectuent aucune action.

    En bas de la fenêtre se trouvent les champs **Subscription Key** (Clé d’abonnement) et **Subscription Region** (Région d’abonnement). Ces champs doivent être remplis avec une clé d’abonnement valide et la région correcte pour cette clé. Pour obtenir une clé d’abonnement, consultez [Abonnements](https://azure.microsoft.com/try/cognitive-services/). Si vous avez obtenu votre clé d’abonnement avec l’essai gratuit disponible à ce lien, la région par défaut **westcentralus** est la région appropriée pour vos clés d’abonnement.

1. Quittez l’application du tutoriel.

## <a name="add-the-tutorial-code-to-the-project"></a>Ajouter le code du didacticiel au projet

L’application Java Swing est configurée avec six onglets. Chaque onglet illustre une fonctionnalité différente de Vision par ordinateur (analyse, OCR, etc.). Les six sections du didacticiel n’ont pas de dépendances entre elles. Vous pouvez donc ajouter une section, la totalité des six sections ou n’importe quel sous-ensemble. Vous pouvez ajouter les sections dans n’importe quel ordre.

### <a name="analyze-an-image"></a>Analyser une image

La fonctionnalité Analyser de Vision par ordinateur scanne une image pour détecter plus de 2 000 objets, êtres vivants, paysages et actions reconnaissables. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui décrit l’image avec des balises descriptives, une analyse des couleurs, des légendes et bien plus encore.

Pour compléter la fonctionnalité Analyser de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

La méthode du gestionnaire d’événements **analyzeImageButtonActionPerformed** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la méthode **AnalyzeImage** pour analyser l’image. Quand **AnalyzeImage** retourne le résultat, la méthode affiche la réponse JSON mise en forme dans la zone de texte **Response** (Réponse), extrait la première légende de **JSONObject**, et affiche la légende et son niveau de confiance.

Copiez et collez le code suivant dans la méthode **analyzeImageButtonActionPerformed**.

> [!NOTE]
> NetBeans ne permet pas de coller le code sur la ligne de définition (```private void```) ou l’accolade fermante de la méthode. Pour copier le code, copiez les lignes situées entre la définition et l’accolade fermante de la méthode, et collez-les sur le contenu de la méthode.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La méthode **AnalyzeImage** inclut l’appel d’API REST dans un wrapper pour analyser une image. La méthode retourne un **JSONObject** décrivant l’image, ou une valeur **Null** si une erreur s’est produite.

Copiez et collez la méthode **AnalyzeImage** juste en dessous de la méthode **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-application"></a>Exécution de l'application

Appuyez sur **F6** pour exécuter l’application. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Entrez une URL vers une image à analyser, puis cliquez sur le bouton **Analyze Image** (Analyser l’image) pour analyser une image et voir le résultat.

### <a name="recognize-a-landmark"></a>Reconnaître un élément géographique

La fonctionnalité Élément géographique de Vision par ordinateur analyse une image pour détecter des éléments géographiques naturels et construits par l’homme, tels que des montagnes ou des bâtiments célèbres. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui identifie les éléments géographiques trouvés dans l’image.

Pour compléter la fonctionnalité Élément géographique de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

La méthode du gestionnaire d’événements **landmarkImageButtonActionPerformed** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la méthode **LandmarkImage** pour analyser l’image. Quand **LandmarkImage** retourne le résultat, la méthode affiche la réponse JSON mise en forme dans la zone de texte **Response** (Réponse), extrait le nom du premier élément géographique de **JSONObject**, et affiche l’élément géographique et son niveau de confiance dans la fenêtre.

Copiez et collez le code suivant dans la méthode **landmarkImageButtonActionPerformed**.

> [!NOTE]
> NetBeans ne permet pas de coller le code sur la ligne de définition (```private void```) ou l’accolade fermante de la méthode. Pour copier le code, copiez les lignes situées entre la définition et l’accolade fermante de la méthode, et collez-les sur le contenu de la méthode.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La méthode **LandmarkImage** inclut l’appel d’API REST dans un wrapper pour analyser une image. La méthode retourne un **JSONObject** décrivant les éléments géographiques trouvés dans l’image, ou une valeur **Null** si une erreur s’est produite.

Copiez et collez la méthode **LandmarkImage** juste en dessous de la méthode **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Exécution de l'application

Appuyez sur **F6** pour exécuter l’application. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Cliquez sur l’onglet **Landmark** (Élément géographique), entrez une URL vers une image d’élément géographique, puis cliquez sur le bouton **Analyze Image** (Analyser l’image) pour analyser une image et voir le résultat.

### <a name="recognize-celebrities"></a>Reconnaître les célébrités

La fonctionnalité Célébrités de Vision par ordinateur analyse une image pour détecter des personnes célèbres. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui identifie les célébrités trouvées dans l’image.

Pour compléter la fonctionnalité Célébrités de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

La méthode du gestionnaire d’événements **celebritiesImageButtonActionPerformed** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la méthode **CelebritiesImage** pour analyser l’image. Quand **CelebritiesImage** retourne le résultat, la méthode affiche la réponse JSON mise en forme dans la zone de texte **Response** (Réponse), extrait le nom de la première célébrité de **JSONObject**, et affiche la célébrité et son niveau de confiance dans la fenêtre.

Copiez et collez le code suivant dans la méthode **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> NetBeans ne permet pas de coller le code sur la ligne de définition (```private void```) ou l’accolade fermante de la méthode. Pour copier le code, copiez les lignes situées entre la définition et l’accolade fermante de la méthode, et collez-les sur le contenu de la méthode.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La méthode **CelebritiesImage** inclut l’appel d’API REST dans un wrapper pour analyser une image. La méthode retourne un **JSONObject** décrivant les célébrités trouvées dans l’image, ou une valeur **Null** si une erreur s’est produite.

Copiez et collez la méthode **CelebritiesImage** juste en dessous de la méthode **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Exécution de l'application

Appuyez sur **F6** pour exécuter l’application. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Cliquez sur l’onglet **Celebrities** (Célébrités), entrez une URL vers une image de célébrité, puis cliquez sur le bouton **Analyze Image** (Analyser l’image) pour analyser une image et voir le résultat.

### <a name="intelligently-generate-a-thumbnail"></a>Générer une miniature de façon intelligente

La fonctionnalité Miniature de Vision par ordinateur génère une miniature à partir d’une image. À l’aide de la fonctionnalité **Rognage intelligent**, la fonctionnalité Miniature va identifier la zone d’intérêt dans une image et centrer la miniature sur cette zone pour générer des images miniatures plus agréables d’un point de vue esthétique.

Pour compléter la fonctionnalité Miniature de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

La méthode du gestionnaire d’événements **thumbnailImageButtonActionPerformed** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la méthode **getThumbnailImage** pour créer la miniature. Quand **getThumbnailImage** retourne le résultat, la méthode affiche la miniature générée.

Copiez et collez le code suivant dans la méthode **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> NetBeans ne permet pas de coller le code sur la ligne de définition (```private void```) ou l’accolade fermante de la méthode. Pour copier le code, copiez les lignes situées entre la définition et l’accolade fermante de la méthode, et collez-les sur le contenu de la méthode.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La méthode **getThumbnailImage** inclut l’appel d’API REST dans un wrapper pour analyser une image. La méthode retourne un **BufferedImage** qui contient la miniature, ou une valeur **Null** si une erreur s’est produite. Le message d’erreur est retourné dans le premier élément du tableau de chaînes **jsonError**.

Copiez et collez la méthode **getThumbnailImage** suivante juste en dessous de la méthode **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Exécution de l'application

Appuyez sur **F6** pour exécuter l’application. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Cliquez sur l’onglet **Thumbnail** (Miniature), entrez une URL vers une image, puis cliquez sur le bouton **Generate Thumbnail** (Générer la miniature) pour analyser une image et voir le résultat.

### <a name="read-printed-text-ocr"></a>Lire du texte imprimé (OCR)

La fonctionnalité Reconnaissance optique des caractères (OCR) de Vision par ordinateur analyse une image de texte imprimé. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui contient le texte et l’emplacement du texte dans l’image.

Pour compléter la fonctionnalité OCR de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

La méthode du gestionnaire d’événements **ocrImageButtonActionPerformed** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la méthode **OcrImage** pour analyser l’image. Quand **OcrImage** retourne le résultat, la méthode affiche le texte détecté au format JSON dans la zone de texte **Response** (Réponse).

Copiez et collez le code suivant dans la méthode **ocrImageButtonActionPerformed**.

> [!NOTE]
> NetBeans ne permet pas de coller le code sur la ligne de définition (```private void```) ou l’accolade fermante de la méthode. Pour copier le code, copiez les lignes situées entre la définition et l’accolade fermante de la méthode, et collez-les sur le contenu de la méthode.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La méthode **OcrImage** inclut l’appel d’API REST dans un wrapper pour analyser une image. La méthode retourne un **JSONObject** des données JSON retournées de l’appel, ou une valeur **Null** si une erreur s’est produite.

Copiez et collez la méthode **OcrImage** suivante juste en dessous de la méthode **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Exécution de l'application

Appuyez sur **F6** pour exécuter l’application. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Cliquez sur l’onglet **OCR**, entrez une URL vers une image de texte imprimé, puis cliquez sur le bouton **Read Image** (Lire l’image) pour analyser une image et voir le résultat.

### <a name="read-handwritten-text-handwriting-recognition"></a>Lire du texte manuscrit (reconnaissance de l’écriture manuscrite)

La fonctionnalité Reconnaissance de l’écriture manuscrite de Vision par ordinateur analyse une image de texte manuscrit. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui contient le texte et l’emplacement du texte dans l’image.

Pour compléter la fonctionnalité Reconnaissance de l’écriture manuscrite de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

La méthode du gestionnaire d’événements **handwritingImageButtonActionPerformed** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la méthode **HandwritingImage** pour analyser l’image. Quand **HandwritingImage** retourne le résultat, la méthode affiche le texte détecté au format JSON dans la zone de texte **Response** (Réponse).

Copiez et collez le code suivant dans la méthode **handwritingImageButtonActionPerformed**.

> [!NOTE]
> NetBeans ne permet pas de coller le code sur la ligne de définition (```private void```) ou l’accolade fermante de la méthode. Pour copier le code, copiez les lignes situées entre la définition et l’accolade fermante de la méthode, et collez-les sur le contenu de la méthode.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La méthode **HandwritingImage** inclut dans un wrapper les deux appels d’API REST nécessaires pour analyser une image. Étant donné que la reconnaissance de l’écriture manuscrite prend du temps, un processus en deux étapes est utilisé. Le premier appel envoie l’image pour traitement ; le deuxième appel récupère le texte détecté quand le traitement est terminé.

Une fois que le texte est récupéré, la méthode **HandwritingImage** retourne un **JSONObject** décrivant le texte et les emplacements du texte, ou une valeur **Null** si une erreur s’est produite.

Copiez et collez la méthode **HandwritingImage** suivante juste en dessous de la méthode **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Exécution de l'application

Pour exécuter l’application, appuyez sur **F6**. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Cliquez sur l’onglet **Read Handwritten Text** (Lire du texte manuscrit), entrez une URL vers une image de texte manuscrit, puis cliquez sur le bouton **Read Image** (Lire l’image) pour analyser une image et voir le résultat.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel sur l’API Vision par ordinateur C&#35;](CSharpTutorial.md)
- [Tutoriel sur l’API Vision par ordinateur Python](PythonTutorial.md)
