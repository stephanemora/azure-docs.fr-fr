---
title: 'Démarrage rapide : Reconnaissance vocale, Javascript (navigateur) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en JavaScript dans un navigateur avec le kit SDK Speech
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 77afdb491e4a6cee0a3d6861ea76f57502e56278
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "71802433"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en JavaScript dans un navigateur avec le kit SDK Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez apprendre à créer un site web en utilisant la liaison JavaScript du SDK Speech de Cognitive Services en vue de transcrire de la parole en texte.
L’application est basée sur le [kit SDK Speech pour JavaScript](https://aka.ms/csspeech/jsbrowserpackage).

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez [Essayer les services Speech gratuitement](get-started.md).
* Un PC ou un Mac avec un micro qui fonctionne
* Un éditeur de texte
* Une version actuelle de Chrome, Microsoft Edge ou Safari.
* Si vous le souhaitez, un serveur web qui prend en charge les scripts PHP d’hébergement.

## <a name="create-a-new-website-folder"></a>Créer un dossier de site web

Créez un dossier vide. Si vous souhaitez héberger l’exemple sur un serveur web, vérifiez que le serveur web peut accéder au dossier.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Décompresser le SDK Speech pour JavaScript dans ce dossier

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Téléchargez le SDK Speech en tant que [package .zip](https://aka.ms/csspeech/jsbrowserpackage), puis décompressez-le dans le dossier que vous venez de créer. Les deux fichiers suivants sont alors décompressés : `microsoft.cognitiveservices.speech.sdk.bundle.js` et `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Le second est facultatif et est utile pour déboguer le code du SDK.

## <a name="create-an-indexhtml-page"></a>Créer une page index.html

Créez un fichier nommé `index.html` dans le dossier, puis ouvrez-le dans un éditeur de texte.

1. Créez le squelette HTML suivant :

   ```html
   <html>
   <head>
      <title>Speech SDK JavaScript Quickstart</title>
   </head>
   <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
   </body>
   </html>
   ```

1. Ajoutez le code d’interface utilisateur suivant à votre fichier, sous le premier commentaire :

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Ajouter une référence au SDK Speech

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Associez des gestionnaires pour le bouton de reconnaissance, le résultat de la reconnaissance et les champs relatifs à l’abonnement qui sont définis par le code de l’interface utilisateur :

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Créer la source du jeton (facultatif)

Si vous souhaitez héberger la page web sur un serveur web, vous avez la possibilité de fournir une source de jeton pour votre application de démonstration.
De cette façon, votre clé d’abonnement ne quitte jamais le serveur et vous permet d’utiliser les fonctionnalités de reconnaissance vocale sans avoir à entrer de code d’autorisation.

1. Créez un nouveau fichier appelé `token.php`. Dans cet exemple, nous supposons que votre serveur web prend en charge le langage de script PHP. Entrez le code suivant :

   [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Ajoutez le code suivant au fichier `index.html` :

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Les jetons d’autorisation ont une durée de vie limitée.
> Cet exemple simplifié ne montre pas comment actualiser automatiquement les jetons d’autorisation. En tant qu’utilisateur, vous pouvez recharger manuellement la page ou appuyer sur F5 pour l’actualiser.

## <a name="build-and-run-the-sample-locally"></a>Générer et exécuter l’exemple localement

Pour lancer l’application, double-cliquez sur le fichier index.html ou ouvrez-le dans votre navigateur web de votre choix. Celui-ci présente une interface graphique simple, dans laquelle vous pouvez entrer votre clé d’abonnement et votre [région](regions.md), puis déclencher la reconnaissance vocale à l’aide du microphone.

> [!NOTE]
> Cette méthode ne fonctionne pas sur le navigateur Safari.
> Sur Safari, l’exemple de page web doit être hébergé sur un serveur web ; Safari n’autorise pas les sites web chargés depuis un fichier local à utiliser le microphone.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Générer et exécuter l’exemple via un serveur web

Pour lancer l’application, ouvrez le navigateur web de votre choix, accédez à l’URL publique où est hébergé le dossier, entrez votre [région](regions.md), puis déclenchez la reconnaissance à l’aide du microphone. Vous obtiendrez un jeton de votre source de jeton, si cette option est configurée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)
