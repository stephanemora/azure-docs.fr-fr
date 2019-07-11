---
title: 'Démarrage rapide : Reconnaissance vocale, .NET Framework (Windows) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application console de synthèse vocale en utilisant .NET Framework pour Windows et le SDK Speech. Quand vous avez terminé, vous pouvez faire la synthèse vocale à partir d’un texte et entendre ces paroles sur votre haut-parleur en temps réel.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 352d3f5f34bd46d8ee1ba2ed45dcc28af2b430ef
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605023"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Démarrage rapide : Synthèse vocale avec le SDK Speech pour .NET Framework (Windows)

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-dotnet-windows.md) et la [traduction vocale](quickstart-translate-speech-dotnetframework-windows.md).

Utilisez ce guide pour créer une application console de synthèse vocale en utilisant .NET Framework pour Windows et le SDK Speech. Quand vous avez terminé, vous pouvez faire la synthèse vocale à partir d’un texte et entendre ces paroles sur votre haut-parleur en temps réel.

Pour une démonstration rapide (sans créer vous-même le projet Visual Studio comme indiqué ci-dessous) :

Obtenez les derniers [exemples de SDK Speech de Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Pour mener à bien ce projet, vous aurez besoin des éléments suivants :

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Clé d’abonnement pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Un haut-parleur (ou un casque) disponible.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez `Program.cs` et remplacez le code généré automatiquement par cet exemple :

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Recherchez et remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement Speech Services.

1. Recherchez et remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement. Par exemple, si vous utilisez la version d’évaluation gratuite, la région est `westus`.

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-dotnet-windows-08-build.png "Build réussie")

1. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5** pour démarrer l’application.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Démarrer l’application en débogage")

1. Une fenêtre de console apparaît et vous invite à taper du texte. Tapez quelques mots ou une phrase. Le texte que vous avez tapé est transmis aux services Speech et est synthétisé en paroles, qui sont lues sur votre haut-parleur.

    ![Capture d’écran de la sortie de la console après la réussite de la reconnaissance](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Sortie de la console après la réussite de la reconnaissance")

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les polices de la voix](how-to-customize-voice-font.md)
- [Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
