---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: db87f31f4702d3487a11a6d833755105455d74c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400802"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer :

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Créer une ressource Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)
> * Veiller à avoir accès à un microphone pour la capture audio

## <a name="source-code"></a>Code source

Créez un fichier source C++ appelé *helloworld.cpp* et collez-y le code suivant.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explication du code

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Générer et exécuter l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur <kbd>F5</kbd>) pour démarrer l’application **helloworld**.

1. Prononcez une phrase ou quelques mots en anglais. L’application transmet vos paroles au service Speech, qui les transcrit en texte et les renvoie à l’application pour qu’elle les affiche.

   ![Sortie de la console après une reconnaissance réussie](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]