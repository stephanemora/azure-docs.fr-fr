---
title: 'Démarrage rapide : Synthèse vocale, C# (.NET Core) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en C# sous .NET Core sur Windows à l’aide du SDK Speech
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467192"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Démarrage rapide : Synthèse vocale à l’aide du SDK Speech pour .NET Core

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-dotnetcore-windows.md) et la [traduction vocale](quickstart-translate-speech-dotnetcore-windows.md).

Dans cet article, vous créez une application console C# pour .NET Core sur Windows en utilisant le [kit SDK Speech](speech-sdk.md) de Cognitive Services. Vous synthétisez de la voix à partir de texte en temps réel sur les haut-parleurs de votre PC. L’application est créée avec le [package NuGet du kit SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017 (toute édition).

> [!NOTE]
> .NET Core est une plateforme .NET à vocation multiplateforme, open source, qui implémente la spécification [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, voir [Essayer le service Speech gratuitement](get-started.md).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Kit de développement logiciel (SDK) .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez `Program.cs` et remplacez tout le code de ce fichier par ce qui suit.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. De même, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, choisissez **Générer** > **Générer la solution**. Le code doit se compiler sans erreur.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Build réussie")

1. Lancez l’application. Dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Démarrer l’application en débogage")

1. Une fenêtre de console s’affiche, vous invitant à taper du texte. Tapez quelques mots ou une phrase. Le texte que vous avez tapé est transmis aux services Speech et est synthétisé en paroles, qui sont lues sur votre haut-parleur.

    ![Capture d’écran de la sortie de la console après la réussite de la synthèse](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Sortie de la console après la réussite de la synthèse")

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment enregistrer la synthèse vocale dans un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les polices de la voix](how-to-customize-voice-font.md)
- [Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
