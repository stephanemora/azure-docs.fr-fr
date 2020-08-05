---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 67d0ab9e1a99d0eca937dad410eb29c52cda6fc2
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405180"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer :

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Créer une ressource Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>Commencer avec du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet Créez un fichier index.js et ajoutez ce code.

Veillez à renseigner vos valeurs pour `subscriptionKey`, `servcieRegion` et `filename`.

```JavaScript
(function() {
  // <code>
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
 
}());
  
```
## <a name="load-the-file-into-an-pullaudiooutputstream"></a>Charger le fichier dans un PullAudioOutputStream

Pour NodeJS, le Kit de développement logiciel (SDK) Speech ne prend pas directement en charge l’accès au fichier de manière native. Nous allons donc ouvrir le fichier et y écrire à l’aide d’un `PullAudioOutputStream`.


```JavaScript
// create the push stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and push it to the push stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
```

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Avant de pouvoir initialiser un objet `SpeechSynthesizer`, vous devez créer une configuration qui utilise votre clé d’abonnement et la région de votre abonnement. Ensuite, insérez ce code.

> [!NOTE]
> Le SDK Speech reconnaît par défaut l’utilisation de la langue en-US. Consultez [Spécifier la langue source pour la reconnaissance vocale](../../../../how-to-specify-source-language.md) pour plus d’informations sur le choix de la langue source.

 ```JavaScript
   // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
```
## <a name="create-an-audio-configuration"></a>Créer une configuration audio

À présent, vous devez créer un objet `AudioConfig` qui pointe vers votre `PullAudioOutputStream`. Insérez ce code juste en dessous de votre configuration Speech.

```JavaScript
    var audioConfig = sdk.AudioConfig.fromStreamInput(pullStream);
```

## <a name="initialize-a-speechsynthesizer"></a>Initialiser un SpeechSynthesizer

À présent, créons l’objet `SpeechSynthesizer` à l’aide des objets `SpeechConfig` et `AudioConfig` créés précédemment.

```JavaScript
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
```
## <a name="recognize-a-phrase-and-display-results"></a>Reconnaître une expression et afficher les résultats

À partir de l’objet `SpeechSynthesizer`, vous devez appeler la méthode `speakTextAsync()`. Cette méthode permet au service Speech de savoir que vous envoyez du texte à des fins de synthèse.

Nous allons également écrire le résultat retourné, ou toute erreur, sur la console et enfin fermer le synthétiseur.
```JavaScript
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);
```
## <a name="check-your-code"></a>Vérifier votre code
```JavaScript
(function() {
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
  
  // create the pull stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and write it to the pull stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
 
  // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
  var audioConfig = sdk.AudioConfig.fromStreamOutput(pullStream);
  
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);

}());
```
## <a name="run-the-sample-locally"></a>Télécharger l’exemple localement

Exécuter le code à l’aide de NodeJs
```bash
node index.js
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]