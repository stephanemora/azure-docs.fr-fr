---
title: 'Démarrage rapide : traduire une entrée vocale avec C# (UWP) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer une application UWP (plateforme Windows universelle) simple pour capturer les paroles de l’utilisateur, les traduire dans une autre langue et générer le texte à la ligne de commande. Ce guide est conçu pour les utilisateurs de Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 108edfeb7bfe24184219e0011f054c36c22c9890
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602785"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Démarrage rapide : traduire une entrée vocale à l’aide du SDK Speech pour C# (UWP)

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-uwp.md), la [traduction vocale](quickstart-text-to-speech-csharp-uwp.md) et [l’assistant virtuel « voice-first »](quickstart-virtual-assistant-csharp-uwp.md).

Dans ce guide de démarrage rapide, vous allez créer une application UWP (plateforme Windows universelle) simple qui capture les paroles de l’utilisateur à partir du microphone de votre ordinateur, les traduit, puis transcrit le texte traduit à la ligne de commande en temps réel. Cette application est conçue pour fonctionner sur Windows 64 bits et est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017.

Pour obtenir la liste complète des langues disponibles pour la traduction vocale, consultez [Prise en charge linguistique](language-support.md).

> [!NOTE]
> La plateforme Windows universelle (UWP) vous permet de développer des applications qui s’exécutent sur n’importe quel appareil prenant en charge Windows 10, notamment des PC, Xbox, Surface Hub et d’autres appareils.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. L’interface utilisateur de l’application est définie à l’aide de XAML. Ouvrez `MainPage.xaml` dans l’Explorateur de solutions. Dans la vue XAML du concepteur, insérez l’extrait de code XAML suivant entre `<Grid>` et `</Grid>`.

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Ouvrez le fichier source code-behind `MainPage.xaml.cs` (vous le trouvez regroupé sous `MainPage.xaml`). Remplacez tout le code de ce fichier par ce qui suit.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Dans le gestionnaire `SpeechTranslationFromMicrophone_ButtonClicked` de ce fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Dans le gestionnaire `SpeechTranslationFromMicrophone_ButtonClicked`, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez toutes les modifications dans le projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-uwp-08-build.png "Build réussie")

1. Lancez l’application. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-uwp-09-start-debugging.png "Démarrer l’application en débogage")

1. Une fenêtre s’affiche. Sélectionnez **Enable Microphone** (Activer le microphone) et confirmez la demande d’autorisation qui s’affiche.

    ![Capture d’écran de la demande d’autorisation](media/sdk/qs-csharp-uwp-10-access-prompt.png "Démarrer l’application en débogage")

1. Sélectionnez **Speech recognition with microphone input** (Reconnaissance vocale avec entrée par micro) et prononcez une phrase ou quelques mots en anglais dans le micro de votre appareil. Votre production orale est transmise au service Speech, et transcrite en texte qui apparaît dans la fenêtre.

    ![Capture d’écran de l’interface utilisateur de la reconnaissance vocale](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
