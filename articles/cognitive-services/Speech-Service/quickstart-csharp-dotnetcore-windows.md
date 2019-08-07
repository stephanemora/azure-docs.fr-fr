---
title: 'Démarrage rapide : Reconnaissance vocale, C# (.NET Core) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer la reconnaissance vocale en C# sous .NET Core sur Windows ou macOS en utilisant le SDK Speech
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 341f173e399196e32ab59f44db4302b1b1a195e6
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607731"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Démarrage rapide : reconnaissance vocale à l’aide du SDK Speech pour .NET Core

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](quickstart-text-to-speech-dotnetcore.md) et la [traduction vocale](quickstart-translate-speech-dotnetcore-windows.md).

Si vous le souhaitez, choisissez un autre langage de programmation et/ou environnement :<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console C# pour .NET Core sur Windows ou macOS en utilisant le [SDK Speech](speech-sdk.md) de Cognitive Services. Vous transcrivez la reconnaissance vocale en temps réel à partir du microphone de votre PC. L’application est créée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017 ou version ultérieure (toute édition).

> [!NOTE]
> .NET Core est une plateforme .NET à vocation multiplateforme, open source, qui implémente la spécification [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, voir [Essayer le service Speech gratuitement](get-started.md).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Kit de développement logiciel (SDK) .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou version ultérieure
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez `Program.cs` et remplacez tout le code de ce fichier par ce qui suit.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. De même, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, choisissez **Générer** > **Générer la solution**. Le code doit se compiler sans erreur.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Build réussie")

1. Lancez l’application. Dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Démarrer l’application en débogage")

1. Une fenêtre de console s’affiche vous invitant à dire quelque chose. Prononcez une phrase ou quelques mots en anglais. Votre production orale est transmise au service Speech, et transcrite en texte qui apparaît dans la même fenêtre.

    ![Capture d’écran de la sortie de la console après la réussite de la reconnaissance](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Sortie de la console après la réussite de la reconnaissance")

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
