---
title: 'Démarrage rapide : Reconnaissance vocale, C++ (Windows) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en C++ sur Windows Desktop avec le SDK Speech
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383064"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale en C++ sur Windows avec le SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-cpp-windows.md) et la [traduction vocale](quickstart-translate-speech-cpp-windows.md).

Dans cet article, vous créez une application console C++ pour Windows. Vous utilisez le [SDK Speech](speech-sdk.md) de Cognitive Services pour faire la synthèse vocale à partir d’un texte en temps réel et pour lire les paroles sur les haut-parleurs de votre PC. L’application est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2019 (n’importe quelle édition).

Pour obtenir la liste complète des langues/voix disponibles pour la synthèse vocale, consultez [Prise en charge des langues](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, consultez [Essayer le service Speech gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source **helloworld.cpp**.

1. Remplacez tout le code par l’extrait de code suivant :

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application **helloworld**.

1. Tapez une phrase ou quelques mots en anglais. L’application transmet votre texte aux services Speech, qui envoient une synthèse vocale à l’application pour qu’elle la lise sur votre haut-parleur.

   ![Sortie de la console après une synthèse vocale réussie](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment enregistrer la synthèse vocale dans un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C++ sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](record-custom-voice-samples.md)
