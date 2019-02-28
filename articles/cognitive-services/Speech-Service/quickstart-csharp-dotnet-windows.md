---
title: 'Démarrage rapide : Reconnaissance vocale, .NET Framework (Windows) - Services de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application de console de reconnaissance vocale à l’aide de .NET Framework pour Windows et du kit SDK Speech. Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: cff3f526586eb52466883247d41a57dae919dd21
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867216"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Démarrage rapide : reconnaissance vocale à l’aide du SDK Speech pour le .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Utilisez ce guide pour créer une application de console de reconnaissance vocale à l’aide de .NET Framework pour Windows et du kit SDK Speech. Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.

## <a name="prerequisites"></a>Prérequis

Pour mener à bien ce projet, vous aurez besoin des éléments suivants :

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Clé d’abonnement pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Accès au microphone de l’ordinateur

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez `Program.cs` et remplacez le code généré automatiquement par cet exemple :

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Recherchez et remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement au service Speech.

1. Recherchez et remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement. Par exemple, si vous utilisez la version d’évaluation gratuite, la région est `westus`.

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-dotnet-windows-08-build.png "Build réussie")

1. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5** pour démarrer l’application.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Démarrer l’application en débogage")

1. Une fenêtre de console s’affiche et vous invite à parler. Maintenant, dites quelque chose en français. Votre production orale est transmise au service Speech, et transcrite en texte en temps réel. Le résultat est inscrit sur la console.

    ![Capture d’écran de la sortie de la console après la réussite de la reconnaissance](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Sortie de la console après la réussite de la reconnaissance")

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
