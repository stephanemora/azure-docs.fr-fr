---
title: 'Démarrage rapide : Synthèse vocale, C# (UWP) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous créez une application Universal Windows Platform (UWP) C# en utilisant le kit SDK Speech de Cognitive Services. Vous synthétisez de la voix à partir de texte en temps réel sur les haut-parleurs de votre appareil. L’application est créée avec le package NuGet du kit SDK Speech et Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 5e1f8aea1b00cbba7fec6c7ca416a965458ab526
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607759"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale dans une application UWP, à l’aide du kit SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-uwp.md), la [traduction vocale](quickstart-translate-speech-uwp.md) et l’[assistant virtuel « voice-first »](quickstart-virtual-assistant-csharp-uwp.md).

Dans cet article, vous développez une application Universal Windows Platform (UWP, version Windows 1709 ou ultérieure) C# à l’aide du [SDK Speech](speech-sdk.md) de Cognitive Services. Le programme synthétise de la voix à partir de texte en temps réel sur les haut-parleurs de votre appareil. L’application est créée avec le [package NuGet du kit SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017 ou version ultérieure (toute édition).

> [!NOTE]
> La plateforme Windows universelle vous permet de développer des applications qui s’exécutent sur n’importe quel appareil prenant en charge Windows 10, notamment des PC, Xbox, Surface Hub et d’autres appareils.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou version ultérieure
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. L’interface utilisateur de l’application est définie à l’aide de XAML. Ouvrez `MainPage.xaml` dans l’Explorateur de solutions. Dans la vue XAML du concepteur, insérez l’extrait de code XAML suivant dans la balise de grille (entre `<Grid>` et `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Ouvrez le fichier source code-behind `MainPage.xaml.cs` (vous le trouvez regroupé sous `MainPage.xaml`). Remplacez tout le code de ce fichier par ce qui suit.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Dans le gestionnaire `Speak_ButtonClicked` de ce fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Dans le gestionnaire `Speak_ButtonClicked`, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez toutes les modifications dans le projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-uwp-08-build.png "Build réussie")

1. Lancez l’application. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-uwp-09-start-debugging.png "Démarrer l’application en débogage")

1. Entrez du texte dans la zone de texte, puis cliquez sur **Speak**. Votre texte est transmis aux services Speech et est synthétisé en paroles, qui sont lues sur votre haut-parleur.

    ![Capture d’écran de l’interface utilisateur de la synthèse vocale](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les polices de la voix](how-to-customize-voice-font.md)
- [Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
