---
title: 'Démarrage rapide : Reconnaître la parole à partir d’un fichier audio, C++ (macOS) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en C++ sur macOS avec le SDK Speech
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: a3a651286fec9328c6d1835f68a4651212b5bd8c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506370"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, effectuez les étapes suivantes :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=macos)

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Créez un fichier source C++ nommé `helloworld.cpp`, et collez le code suivant dedans.

   ````C++

    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech recognizer using a WAV file. The default language is "en-us".
    // Replace with your own audio file name.
    auto audioInput = AudioConfig::FromWavFileInput("whatstheweatherlike.wav");
    auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
    cout << "Recognizing first result...\n";

    // Starts speech recognition, and returns after a single utterance is recognized. The end of a
    // single utterance is determined by listening for silence at the end or until a maximum of 15
    // seconds of audio is processed.  The task returns the recognition text as result.
    // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
    // shot recognition like command or query.
    // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
    auto result = recognizer->RecognizeOnceAsync().get();

    // Checks result.
    if (result->Reason == ResultReason::RecognizedSpeech)
    {
        cout << "RECOGNIZED: Text=" << result->Text << std::endl;
    }
    else if (result->Reason == ResultReason::NoMatch)
    {
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
    }
    else if (result->Reason == ResultReason::Canceled)
    {
        auto cancellation = CancellationDetails::FromResult(result);
        cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

        if (cancellation->Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
            cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
            cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }

   ````

1. Dans ce nouveau fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement aux services Voix.

1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Remplacez la chaîne `whatstheweatherlike.wav` par le nom de votre fichier.


## <a name="build-the-app"></a>Générer l’application

> [!NOTE]
> Veillez à entrer les commandes ci-dessous en tant que _ligne de commande unique_. Le moyen le plus simple pour ce faire consiste à copier la commande à l’aide du bouton **Copier** situé en regard de chaque commande, et de la coller dans votre invite de l’interpréteur de commandes.

* Exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Exécution de l'application

1. Configurez le chemin de la bibliothèque du chargeur pour qu’il pointe vers la bibliothèque du kit SDK Speech.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Exécutez l'application.

   ```sh
   ./helloworld
   ```

1. Votre fichier audio est transmis aux services Speech et le premier énoncé du fichier est transcrit en texte, qui apparaît dans la même fenêtre.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]