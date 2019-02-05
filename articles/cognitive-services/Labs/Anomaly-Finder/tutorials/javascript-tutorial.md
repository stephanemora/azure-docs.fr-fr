---
title: 'Didacticiel : Détection d’anomalie avec JavaScript'
titlesuffix: Azure Cognitive Services
description: Explorez une application web JavaScript qui utilise l’API Détection d’anomalies. Envoyez des points de données d’origine à l’API et obtenez la valeur attendue et des points d’anomalies.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 9e66b24987b2318f3022404d951fbb911e7b592d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227826"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>Tutoriel : Détection d’anomalie avec une application JavaScript

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Explorez une application web qui utilise l’API REST Détection d’anomalies pour détecter une anomalie. L’exemple envoie les données de série chronologique à l’API Détection d’anomalies avec votre clé d’abonnement, puis obtient tous les points d’anomalies et la valeur attendue pour chaque point de données à partir de l’API.

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Plateforme requise

Ce tutoriel a été développé à l’aide d’un éditeur de texte simple.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>S’abonner à la détection d’anomalies et obtenir une clé d’abonnement 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Obtenir et utiliser l’exemple

Ce tutoriel présente deux scénarios de détection d’anomalies dans des données de série chronologique. Allons-y.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Télécharger le projet du tutoriel

Clonez le [tutoriel Détection d’anomalies JavaScript Cognitive Services](https://github.com/MicrosoftAnomalyDetection/javascript-sample) ou téléchargez le fichier .zip et extrayez-le dans un répertoire vide.

<a name="Step2"></a>
### <a name="run-the-example"></a>Exécuter l’exemple

Vous pouvez essayer l’exemple dans deux scénarios.
1. Placez votre **clé d’abonnement** dans le champ subscriptionKey de la fonction detect dans anomalydetection.html.
2. Placez le point de terminaison d’API de détection d’anomalies et vérifiez que la bonne région d’abonnement est spécifiée.
3. Ouvrez le fichier **anomalydetection.html** dans un navigateur web.

**Scénario 1 Détecter des données de série chronologique hebdomadaires**
1. Dans le champ Period, entrez la période **7**. 
2. Remplacez les exemples de données par vos points de données de série chronologique hebdomadaires (Json) dans le champ Points, ou utilisez les exemples de données directement.
3. Cliquez sur le bouton Anomaly Detection et vérifiez le résultat dans la zone de texte Response appropriée.

**Scénario 2 Détecter les données de série chronologique sans période**
1. Supposez que vous ne connaissez pas la période de la série chronologique et laissez le champ period vide.
2. Utilisez les mêmes données de série chronologique que pour le scénario 1.
3. Cliquez sur le bouton Anomaly Detection et vérifiez le champ Period dans la zone de texte Response appropriée.

<a name="Step3"></a>
### <a name="read-the-result"></a>Lire le résultat

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Passer en revue et apprendre

Vous avez maintenant une application opérationnelle. Examinons comment l’exemple d’application s’intègre à la technologie Cognitive Services. Cette étape vous permettra soit de poursuivre la création de cette application, soit de développer votre propre application à l’aide de la Détection d’anomalies Microsoft.
Cet exemple d’application utilise le point de terminaison d’API Restful de Détection d’anomalies.
Examinons un extrait de code d’anomalydetection.html pour voir comment l’API Restful est utilisée dans l’exemple d’application.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
