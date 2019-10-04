---
title: 'Démarrage rapide : Synthèse vocale, C# (UWP) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous créez une application Universal Windows Platform (UWP) C# en utilisant le kit SDK Speech de Cognitive Services. Vous synthétisez de la voix à partir de texte en temps réel sur les haut-parleurs de votre appareil. L’application est créée avec le package NuGet du SDK Speech et Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382221"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Démarrage rapide : Synthèse vocale dans une application UWP, à l’aide du kit SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-uwp.md), la [traduction vocale](quickstart-translate-speech-uwp.md) et l’[assistant virtuel « voice-first »](quickstart-virtual-assistant-csharp-uwp.md).

Dans cet article, vous développez une application UWP C# en utilisant le [SDK Speech](speech-sdk.md) de Cognitive Services. Le programme synthétise de la voix à partir de texte en temps réel sur les haut-parleurs de votre appareil. Vous générez l’application avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2019 (n’importe quelle édition).

> [!NOTE]
> La plateforme Windows universelle vous permet de développer des applications qui s’exécutent sur n’importe quel appareil prenant en charge Windows 10, notamment des PC, Xbox, Surface Hub et d’autres appareils.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

Ajoutez maintenant le code XAML qui définit l’interface utilisateur de l’application, puis ajoutez l’implémentation du code-behind C#.

1. Dans l’**Explorateur de solutions**, ouvrez `MainPage.xaml`.

1. Dans la vue XAML du concepteur, insérez l’extrait de code XAML suivant dans la balise **Grid** (entre `<Grid>` et `</Grid>`) :

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Dans l’**Explorateur de solutions**, ouvrez le fichier source code-behind `MainPage.xaml.cs`. (Il se trouve sous `MainPage.xaml`.)

1. Remplacez tout le code de ce fichier par l’extrait de code suivant :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Dans le gestionnaire `Speak_ButtonClicked` du fichier source, recherchez la chaîne `YourSubscriptionKey`, puis remplacez-la par la clé de votre abonnement.

1. Dans le gestionnaire `Speak_ButtonClicked`, recherchez la chaîne `YourServiceRegion`, puis remplacez-la par la [région](regions.md) associée à votre abonnement. (Par exemple, utilisez `westus` pour l’abonnement d’essai gratuit.)

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout** pour enregistrer vos modifications.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

Vous êtes maintenant prêt à générer et à tester votre application.

1. Dans la barre de menus, choisissez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application. La fenêtre **helloworld** s’affiche.

   ![Exemple d’application de synthèse vocale UWP en C# - Démarrage rapide](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Entrez du texte dans la zone de texte, puis cliquez sur **Speak**. Votre texte est transmis aux services Speech et est synthétisé en paroles, qui sont lues sur votre haut-parleur.

    ![Interface utilisateur de la synthèse vocale](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Créer et utiliser des modèles Custom Voice](how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
