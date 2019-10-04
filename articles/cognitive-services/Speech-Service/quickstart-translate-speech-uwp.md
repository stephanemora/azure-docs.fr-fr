---
title: 'Démarrage rapide : traduire une entrée vocale avec C# (UWP) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer une application UWP (Plateforme Windows universelle) pour capturer les paroles de l’utilisateur, les traduire dans une autre langue et afficher le texte sur la ligne de commande. Ce guide est conçu pour les utilisateurs de Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382645"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Démarrage rapide : traduire une entrée vocale à l’aide du SDK Speech pour C# (UWP)

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-uwp.md), la [synthèse vocale](quickstart-text-to-speech-csharp-uwp.md) et l’[assistant virtuel « voice-first »](quickstart-virtual-assistant-csharp-uwp.md).

Dans ce guide de démarrage rapide, vous allez créer une application UWP (Plateforme Windows universelle) qui capture les paroles de l’utilisateur à partir du microphone de votre ordinateur, les traduit, puis transcrit le texte traduit sur la ligne de commande en temps réel. Cette application est conçue pour fonctionner sur Windows 64 bits, et elle est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2019.

Pour obtenir la liste complète des langues disponibles pour la traduction vocale, consultez [Prise en charge linguistique](language-support.md).

> [!NOTE]
> La plateforme Windows universelle (UWP) vous permet de développer des applications qui s’exécutent sur n’importe quel appareil prenant en charge Windows 10, notamment des PC, Xbox, Surface Hub et d’autres appareils.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Dans l’**Explorateur de solutions**, ouvrez le fichier source code-behind `MainPage.xaml.cs`. (Il se trouve sous `MainPage.xaml`.)

1. Remplacez tout le code de ce fichier par l’extrait de code suivant :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Dans le gestionnaire `SpeechTranslationFromMicrophone_ButtonClicked` de ce fichier, recherchez la chaîne `YourSubscriptionKey`, puis remplacez-la par la clé de votre abonnement.

1. Dans le gestionnaire `SpeechTranslationFromMicrophone_ButtonClicked`, recherchez la chaîne `YourServiceRegion`, puis remplacez-la par la [région](regions.md) associée à votre abonnement. (Par exemple, utilisez `westus` pour l’abonnement d’essai gratuit.)

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout** pour enregistrer vos modifications.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

Vous êtes maintenant prêt à générer et à tester votre application.

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application. La fenêtre **helloworld** s’affiche.

   ![Exemple d’application de traduction UWP en C# - Démarrage rapide](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Sélectionnez **Activer le microphone** et, quand la demande d’autorisation d’accès apparaît, sélectionnez **Oui**.

   ![Demande d’autorisation d’accès au microphone](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Sélectionnez **Reconnaissance vocale avec entrée au microphone**, et prononcez une phrase ou quelques mots en anglais dans le micro de votre appareil. L’application transmet vos paroles au service Speech, qui convertit la parole en texte dans une autre langue (dans ce cas, l’allemand). Le service Speech renvoie le texte traduit à l’application, qui affiche la traduction dans la fenêtre.

   ![Interface utilisateur de traduction vocale](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Entraîner un modèle pour Custom Speech](how-to-custom-speech-train-model.md)
