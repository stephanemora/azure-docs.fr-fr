---
title: 'Tutoriel : Effectuer des opérations sur les images - JavaScript'
titlesuffix: Azure Cognitive Services
description: Explorez une application JavaScript de base qui utilise l’API Vision par ordinateur dans Azure Cognitive Services. Effectuez une reconnaissance OCR, créez des miniatures et utilisez des fonctionnalités visuelles dans une image.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 1ad25b383e3d275324b57ae1c343560c82b2a3a2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215229"
---
# <a name="tutorial-computer-vision-api-javascript"></a>Tutoriel : API Vision par ordinateur avec JavaScript

Ce tutoriel montre les fonctionnalités de l’API REST Vision par ordinateur dans Azure Cognitive Services.

Explorez une application JavaScript qui utilise l’API REST Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Cet exemple vous permet d’envoyer une URL d’image à des fins d’analyse ou de traitement. Vous pouvez utiliser cet exemple open source comme modèle pour générer votre propre application JavaScript utilisant l’API REST Vision par ordinateur.

L’application de formulaire JavaScript a déjà été écrite, mais sans fonctionnalité Vision par ordinateur. Dans ce tutoriel, vous ajoutez le code spécifique à l’API REST Vision par ordinateur pour compléter la fonctionnalité de l’application.

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Plateforme requise

Ce tutoriel a été développé à l’aide d’un éditeur de texte simple.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>S’abonner à l’API Vision par ordinateur et obtenir une clé d’abonnement 

Avant de créer l’exemple, vous devez vous abonner à l’API Vision par ordinateur qui fait partie d’Azure Cognitive Services. Pour plus d’informations sur l’abonnement et la gestion des clés, consultez [Abonnements](https://azure.microsoft.com/try/cognitive-services/). Les clés primaire et secondaire peuvent toutes deux être utilisées dans ce tutoriel. 

## <a name="acquire-the-incomplete-tutorial-project"></a>Acquérir le projet du didacticiel incomplet

### <a name="download-the-tutorial-project"></a>Télécharger le projet du tutoriel

Clonez le [tutoriel Vision par ordinateur JavaScript Cognitive Services](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial) ou téléchargez le fichier .zip et extrayez-le dans un répertoire vide.

Si vous préférez utiliser le tutoriel terminé avec tout le code du tutoriel ajouté, vous pouvez utiliser les fichiers dans le dossier **Completed**.

## <a name="add-the-tutorial-code-to-the-project"></a>Ajouter le code du didacticiel au projet

L’application JavaScript est configurée avec six fichiers .html, un pour chaque fonctionnalité. Chaque fichier illustre une fonction différente de Vision par ordinateur (analyse, OCR, etc.). Les six sections du tutoriel n’ayant pas d’interdépendances, vous pouvez ajouter le code du tutoriel à un fichier, aux six fichiers ou seulement à deux fichiers. Vous pouvez également ajouter le code du tutoriel aux fichiers dans n’importe quel ordre.

Allons-y.

### <a name="analyze-an-image"></a>Analyser une image

La fonctionnalité Analyser de Vision par ordinateur scanne une image pour détecter plus de 2 000 objets, êtres vivants, paysages et actions reconnaissables. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui décrit l’image avec des balises descriptives, une analyse des couleurs, des légendes et bien plus encore.

Pour compléter la fonctionnalité Analyser de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

Ouvrez le fichier **analyze.html** dans un éditeur de texte et recherchez la fonction **analyzeButtonClick** près du bas du fichier.

La fonction du gestionnaire d’événements **analyzeButtonClick** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la fonction **AnalyzeImage** pour analyser l’image.

Copiez et collez le code suivant dans la fonction **analyzeButtonClick**.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La fonction **AnalyzeImage** inclut l’appel d’API REST dans un wrapper pour analyser une image. Lors d’un retour réussi, l’analyse JSON mise en forme s’affichera dans la zone de texte spécifiée et la légende s’affichera dans l’étendue spécifiée.

Copiez et collez le code de la fonction **AnalyzeImage** juste en dessous de la fonction **analyzeButtonClick**.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Exécution de l'application

Enregistrez le fichier **analyze.html** et ouvrez-le dans un navigateur web. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Entrez une URL vers une image à analyser, puis cliquez sur le bouton **Analyze Image** (Analyser l’image) pour analyser une image et voir le résultat.

### <a name="recognize-a-landmark"></a>Reconnaître un élément géographique

La fonctionnalité Élément géographique de Vision par ordinateur analyse une image pour détecter des éléments géographiques naturels et construits par l’homme, tels que des montagnes ou des bâtiments célèbres. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui identifie les éléments géographiques trouvés dans l’image.

Pour compléter la fonctionnalité Élément géographique de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

Ouvrez le fichier **landmark.html** dans un éditeur de texte et recherchez la fonction **landmarkButtonClick** près du bas du fichier.

La fonction du gestionnaire d’événements **landmarkButtonClick** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la fonction **IdentifyLandmarks** pour analyser l’image.

Copiez et collez le code suivant dans la fonction **landmarkButtonClick**.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La fonction **IdentifyLandmarks** inclut l’appel d’API REST dans un wrapper pour analyser une image. Lors d’un retour réussi, l’analyse JSON mise en forme s’affichera dans la zone de texte spécifiée et la légende s’affichera dans l’étendue spécifiée.

Copiez et collez le code de la fonction **IdentifyLandmarks** juste en dessous de la fonction **landmarkButtonClick**.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Exécution de l'application

Enregistrez le fichier **landmark.html** et ouvrez-le dans un navigateur web. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Entrez une URL vers une image à analyser, puis cliquez sur le bouton **Analyze Image** (Analyser l’image) pour analyser une image et voir le résultat.

### <a name="recognize-celebrities"></a>Reconnaître les célébrités

La fonctionnalité Célébrités de Vision par ordinateur analyse une image pour détecter des personnes célèbres. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui identifie les célébrités trouvées dans l’image.

Pour compléter la fonctionnalité Célébrités de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

Ouvrez le fichier **celebrities.html** dans un éditeur de texte et recherchez la fonction **celebritiesButtonClick** près du bas du fichier.

La fonction du gestionnaire d’événements **celebritiesButtonClick** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la fonction **IdentifyCelebrities** pour analyser l’image.

Copiez et collez le code suivant dans la fonction **celebritiesButtonClick**.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Exécution de l'application

Enregistrez le fichier **celebrities.html** et ouvrez-le dans un navigateur web. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Entrez une URL vers une image à analyser, puis cliquez sur le bouton **Analyze Image** (Analyser l’image) pour analyser une image et voir le résultat.

### <a name="intelligently-generate-a-thumbnail"></a>Générer une miniature de façon intelligente

La fonctionnalité Miniature de Vision par ordinateur génère une miniature à partir d’une image. À l’aide de la fonctionnalité **Rognage intelligent**, la fonctionnalité Miniature va identifier la zone d’intérêt dans une image et centrer la miniature sur cette zone pour générer des images miniatures plus agréables d’un point de vue esthétique.

Pour compléter la fonctionnalité Miniature de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

Ouvrez le fichier **thumbnail.html** dans un éditeur de texte et recherchez la fonction **thumbnailButtonClick** près du bas du fichier.

La fonction du gestionnaire d’événements **thumbnailButtonClick** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la fonction **getThumbnail** à deux reprises pour créer deux miniatures, une avec un rognage intelligent et l’autre sans.

Copiez et collez le code suivant dans la fonction **thumbnailButtonClick**.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La fonction **getThumbnail** inclut l’appel d’API REST dans un wrapper pour analyser une image. Lors d’un retour réussi, la miniature est affichée dans l’élément img spécifié.

Copiez et collez la fonction **getThumbnail** suivante juste en dessous de la fonction **thumbnailButtonClick**.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-application"></a>Exécution de l'application

Enregistrez le fichier **thumbnail.html** et ouvrez-le dans un navigateur web. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Entrez une URL vers une image à analyser, puis cliquez sur le bouton **Generate Thumbnails** (Générer des miniatures) pour analyser une image et voir le résultat.

### <a name="read-printed-text-ocr"></a>Lire du texte imprimé (OCR)

La fonctionnalité Reconnaissance optique des caractères (OCR) de Vision par ordinateur analyse une image de texte imprimé. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui contient le texte et l’emplacement du texte dans l’image.

Pour compléter la fonctionnalité OCR de l’application du tutoriel, effectuez les étapes suivantes :

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>Étape 1 de l’OCR : Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

Ouvrez le fichier **ocr.html** dans un éditeur de texte et recherchez la fonction **ocrButtonClick** près du bas du fichier.

La fonction du gestionnaire d’événements **ocrButtonClick** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la fonction **ReadOcrImage** pour analyser l’image.

Copiez et collez le code suivant dans la fonction **ocrButtonClick**.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La fonction **ReadOcrImage** inclut l’appel d’API REST dans un wrapper pour analyser une image. Lors d’un retour réussi, l’analyse JSON mise en forme décrivant le texte et l’emplacement du texte s’affichera dans la zone de texte spécifiée.

Copiez et collez la fonction **ReadOcrImage** suivante juste en dessous de la fonction **ocrButtonClick**.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Exécution de l'application

Enregistrez le fichier **ocr.html** et ouvrez-le dans un navigateur web. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Entrez une URL vers une image de texte à lire, puis cliquez sur le bouton **Read Image** (Lire l’image) pour analyser une image et voir le résultat.

### <a name="read-handwritten-text-handwriting-recognition"></a>Lire du texte manuscrit (Reconnaissance de l’écriture manuscrite)

La fonctionnalité Reconnaissance de l’écriture manuscrite de Vision par ordinateur analyse une image de texte manuscrit. Une fois l’analyse terminée, la fonctionnalité retourne un objet JSON qui contient le texte et l’emplacement du texte dans l’image.

Pour compléter la fonctionnalité Reconnaissance de l’écriture manuscrite de l’application du tutoriel, effectuez les étapes suivantes :

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Ajouter le code du gestionnaire d’événements pour le bouton de formulaire

Ouvrez le fichier **handwriting.html** dans un éditeur de texte et recherchez la fonction **handwritingButtonClick** près du bas du fichier.

La fonction du gestionnaire d’événements **handwritingButtonClick** efface le formulaire, affiche l’image spécifiée dans l’URL, puis appelle la fonction **HandwritingImage** pour analyser l’image.

Copiez et collez le code suivant dans la fonction **handwritingButtonClick**.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Ajouter le wrapper pour l’appel d’API REST

La fonction **ReadHandwrittenImage** inclut dans un wrapper les deux appels d’API REST nécessaires pour analyser une image. Étant donné que la reconnaissance de l’écriture manuscrite prend du temps, un processus en deux étapes est utilisé. Le premier appel envoie l’image pour traitement ; le deuxième appel récupère le texte détecté quand le traitement est terminé.

Une fois que le texte est récupéré, l’analyse JSON mise en forme décrivant le texte et l’emplacement du texte s’affichera dans la zone de texte spécifiée.

Copiez et collez la fonction **ReadHandwrittenImage** suivante juste en dessous de la fonction **handwritingButtonClick**.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Exécution de l'application

Enregistrez le fichier **handwriting.html** et ouvrez-le dans un navigateur web. Placez votre clé d’abonnement dans le champ **Clé d’abonnement** et vérifiez que vous utilisez la région correcte dans **Subscription Region** (Région d’abonnement). Entrez une URL vers une image de texte à lire, puis cliquez sur le bouton **Read Image** (Lire l’image) pour analyser une image et voir le résultat.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel sur l’API Vision par ordinateur C&#35;](CSharpTutorial.md)
- [Tutoriel sur l’API Vision par ordinateur Python](PythonTutorial.md)
