---
title: 'Démarrage rapide : Traduction vocale, .C# (.NET Framework Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer une application .NET Framework pour capturer les paroles de l’utilisateur, les traduire dans une autre langue et générer le texte à la ligne de commande. Ce guide est conçu pour les utilisateurs de Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327339"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Démarrage rapide : Traduire une entrée vocale à l’aide du SDK Speech pour .NET Framework (Windows)

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-dotnet-windows.md) et la [synthèse vocale](quickstart-text-to-speech-dotnet-windows.md).

Dans ce guide de démarrage rapide, vous allez créer une application .NET Framework qui capture les paroles de l’utilisateur à partir du microphone de votre ordinateur, les traduit, puis transcrit le texte traduit à la ligne de commande en temps réel. Cette application peut s’exécuter sur Windows 32 bits ou 64 bits et est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2019.

Pour obtenir la liste complète des langues disponibles pour la traduction vocale, consultez [Prise en charge linguistique](language-support.md).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez **Program.cs** et remplacez tout le code existant par le suivant.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Recherchez la chaîne `YourSubscriptionKey`, puis remplacez-la par votre clé d’abonnement.

1. Recherchez la chaîne `YourServiceRegion`, puis remplacez-la par la [région](regions.md) associée à votre abonnement. Par exemple, si vous utilisez l’abonnement d’essai gratuit, la région est `westus`.

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, choisissez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou sélectionnez **F5**) pour démarrer l’application **helloworld**.

1. Dites une expression ou une phrase en anglais dans le microphone de votre appareil. L’application transmet vos paroles au service Speech, qui convertit la parole en texte dans une autre langue (dans ce cas, l’allemand). Le service Speech renvoie le texte traduit à l’application, qui affiche la traduction dans la fenêtre.

   ![Interface utilisateur de traduction vocale](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio et générer un texte traduit en parole de synthèse, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Entraîner un modèle pour Custom Speech](how-to-custom-speech-train-model.md)
