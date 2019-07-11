---
title: 'Démarrage rapide : Reconnaissance vocale, Node.js - Services de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application console de reconnaissance vocale à l’aide du kit SDK Speech pour Node.js. Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9d233de8a9cdd4b9a3637edcd1c6196b4ad16fd2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605131"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Démarrage rapide : Reconnaissance vocale à l’aide du SDK Speech pour Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Cet article explique comment créer un projet Node.js en utilisant la liaison JavaScript du kit SDK Speech pour Azure Cognitive Services afin de transcrire la parole en texte.
L’application est basée sur le [kit SDK Speech pour JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Version actuelle de [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Création d'un projet

Créez un nouveau dossier et initialisez le projet :

```sh
npm init -f
```

Cette commande initialise les fichiers **package.json** avec les valeurs par défaut. Vous serez probablement amené à modifier ce fichier par la suite.

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Ajoutez le kit SDK Speech à votre projet Node.js :

```
npm install microsoft-cognitiveservices-speech-sdk
```

Cette commande télécharge et installe la dernière version du kit SDK Speech et tous les éléments prérequis à partir de **npmjs**. Le kit SDK est installé dans le répertoire `node_modules` à l’intérieur de votre dossier de projet.

## <a name="use-the-speech-sdk"></a>Utiliser le SDK Speech

Créez un nouveau fichier nommé `index.js` dans le dossier, puis ouvrez-le dans un éditeur de texte.

> [!NOTE]
> Dans Node.js, le kit SDK Speech ne prend pas en charge le microphone ni le type de données **Fichier**. Ceux-ci ne sont pris en charge que sur les navigateurs. Utilisez plutôt l’interface **Stream** pour accéder au kit SDK Speech, au moyen de `AudioInputStream.createPushStream()` ou de `AudioInputStream.createPullStream()`.

Dans cet exemple, nous utilisons l’interface `PushAudioInputStream`.

Ajoutez ce code JavaScript :

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Exécution de l'exemple

Pour ouvrir l’application, adaptez `YourSubscriptionKey`, `YourServiceRegion` et `YourAudioFile.wav` à votre configuration. Ensuite, exécutez-la en appelant cette commande :

```sh
node index.js
```

Elle déclenche une reconnaissance à l’aide du nom de fichier fourni. Et elle présente la sortie dans la console.

Exemple de sortie lorsque vous exécutez `index.js` après avoir mis à jour la clé d’abonnement et utilisé le fichier `whatstheweatherlike.wav` :

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installer et utiliser le SDK Speech avec Visual Studio Code

Vous pouvez également exécuter l’exemple à partir de Visual Studio Code. Suivez ces étapes pour installer, ouvrir et exécuter le guide de démarrage rapide :

1. Démarrez Visual Studio Code. Sélectionnez **Ouvrir le dossier**. Accédez alors au dossier de démarrage rapide.

   ![Ouvrir le dossier](media/sdk/qs-js-node-01-open_project.png)

1. Ouvrez un terminal dans Visual Studio Code.

   ![Fenêtre du terminal](media/sdk/qs-js-node-02_open_terminal.png)

1. Exécutez `npm` pour installer les dépendances.

   ![npm install](media/sdk/qs-js-node-03-npm_install.png)

1. Vous êtes maintenant prêt à ouvrir `index.js` et à définir un point d’arrêt.

   ![index.js avec point d’arrêt à la ligne 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Pour démarrer le débogage, appuyez sur F5 ou sélectionnez **Déboguer/Démarrer le débogage** dans le menu.

   ![Menu Déboguer](media/sdk/qs-js-node-05-start_debugging.png)

1. Quand un point d’arrêt est atteint, vous pouvez inspecter la pile des appels et les variables.

   ![Débogueur](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Toute sortie s’affiche dans la fenêtre de la console de débogage.

   ![Console de débogage](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Node.js sur GitHub](https://aka.ms/csspeech/samples)
