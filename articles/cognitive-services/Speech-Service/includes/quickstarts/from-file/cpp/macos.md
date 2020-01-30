---
title: 'Démarrage rapide : Reconnaître la voix d’un fichier audio, C++ (macOS) – Service Speech'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: 92586667675823343eb1f44f054e939c59719379
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748945"
---
## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=macos)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Créez un fichier source C++ nommé `helloworld.cpp`, et collez le code suivant dedans.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Dans ce nouveau fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement au service Speech.

1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Remplacez la chaîne `whatstheweatherlike.wav` par le nom de votre fichier.

> [!NOTE]
> Le kit SDK Speech reconnaît par défaut l’utilisation de la langue en-US. Consultez [Spécifier la langue source pour la reconnaissance vocale](../../../../how-to-specify-source-language.md) pour plus d’informations sur le choix de la langue source.

## <a name="build-the-app"></a>Générer l’application

> [!NOTE]
> Veillez à entrer les commandes ci-dessous en tant que _ligne de commande unique_. Le moyen le plus simple pour ce faire consiste à copier la commande à l’aide du bouton **Copier** situé en regard de chaque commande, et de la coller dans votre invite de l’interpréteur de commandes.

* Exécutez la commande suivante pour générer l’application.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Exécuter l’application

1. Configurez le chemin de la bibliothèque du chargeur pour qu’il pointe vers la bibliothèque du kit SDK Speech.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Exécutez l'application.

   ```sh
   ./helloworld
   ```

1. Votre fichier audio est transmis au service Speech et le premier énoncé du fichier est transcrit en texte, qui apparaît dans la même fenêtre.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
