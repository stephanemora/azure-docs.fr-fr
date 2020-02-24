---
title: 'Démarrage rapide : Reconnaître la voix venant d’un microphone, C++ (Windows) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en C++ sur Windows Desktop à l’aide du SDK Speech
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 5cb7cfc0b4d888406f8574ab620667f160bfa118
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446225"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=windows)
> * Veiller à avoir accès à un microphone pour la capture audio

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source **helloworld.cpp**.

1. Remplacez tout le code par l’extrait de code suivant :

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par l’**identificateur Région** de la [région](https://aka.ms/speech/sdkregion) qui est associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

> [!NOTE]
> Le SDK Speech reconnaît par défaut l’utilisation de la langue en-US. Consultez [Spécifier la langue source pour la reconnaissance vocale](../../../../how-to-specify-source-language.md) pour plus d’informations sur le choix de la langue source.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application **helloworld**.

1. Prononcez une phrase ou quelques mots en anglais. L’application transmet vos paroles au service Speech, qui les transcrit en texte et les renvoie à l’application pour qu’elle les affiche.

   ![Sortie de la console après une reconnaissance réussie](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]