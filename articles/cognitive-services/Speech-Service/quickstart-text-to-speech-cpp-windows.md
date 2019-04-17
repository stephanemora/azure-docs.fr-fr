---
title: 'Démarrage rapide : Reconnaissance vocale, C++ (Windows) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en C++ sur Windows Desktop avec le SDK Speech
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: 732816ce10836d5828e7f325a3eb3fe31627d4f4
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012371"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale en C++ sur Windows avec le SDK Speech

Dans cet article, vous créez une application console C++ pour Windows. Vous utilisez le [SDK Speech](speech-sdk.md) de Cognitive Services pour faire la synthèse vocale à partir d’un texte en temps réel et pour lire les paroles sur les haut-parleurs de votre PC. L’application est créée avec le [package NuGet du kit SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017 (toute édition).

La fonctionnalité décrite dans cet article est disponible dans le [SDK Speech 1.4.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.4.0).

Pour obtenir la liste complète des langues/voix disponibles pour la synthèse vocale, consultez [Prise en charge des langues](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, consultez [Essayer le service Speech gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source *helloworld.cpp*. Remplacez tout le code sous l’instruction include initiale (`#include "stdafx.h"` ou `#include "pch.h"`) par le code suivant :

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, choisissez **Générer** > **Générer la solution**. Le code doit se compiler sans erreur.

   ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-cpp-windows-06-build.png)

1. Lancez l’application. Dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

   ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Une fenêtre de console s’affiche, vous invitant à taper du texte. Tapez quelques mots ou une phrase. Le texte que vous avez tapé est transmis aux services Speech et est synthétisé en paroles, qui sont lues sur votre haut-parleur.

   ![Capture d’écran de la sortie de la console après une synthèse réussie](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment enregistrer la synthèse vocale dans un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C++ sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les polices de la voix](how-to-customize-voice-font.md)
- [Enregistrement d’échantillons vocaux](record-custom-voice-samples.md)
