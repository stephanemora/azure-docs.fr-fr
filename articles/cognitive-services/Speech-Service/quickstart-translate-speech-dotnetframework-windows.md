---
title: 'Démarrage rapide : reconnaissance vocale, .C# (.NET Framework Windows) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer une application .NET Framework simple pour capturer les paroles de l’utilisateur, les traduire dans une autre langue et générer le texte à la ligne de commande. Ce guide est conçu pour les utilisateurs de Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 6bf7c92b0c9b143f9622cf1698fa39b0f45843f1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874526"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework"></a>Démarrage rapide : traduire une entrée vocale à l’aide du SDK Speech pour .NET Framework

Dans ce guide de démarrage rapide, vous allez créer une application .NET Framework simple qui capture les paroles de l’utilisateur à partir du microphone de votre ordinateur, les traduit, puis transcrit le texte traduit à la ligne de commande en temps réel. Cette application est conçue pour fonctionner sur Windows 64 bits et est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017.

Pour obtenir la liste complète des langues disponibles pour la traduction vocale, consultez [Prise en charge linguistique](language-support.md).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez `Program.cs` et remplacez tout le code de ce fichier par ce qui suit.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. De même, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, choisissez **Générer** > **Générer la solution**. Le code doit se compiler sans erreur.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Build réussie")

1. Lancez l’application. Dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Démarrer l’application en débogage")

1. Une fenêtre de console s’affiche vous invitant à dire quelque chose. Prononcez une phrase ou quelques mots en anglais. Vos paroles sont transmises au service Speech, traduites et retranscrites en texte qui apparaît dans la même fenêtre.

    ![Capture d’écran de la sortie de la console après la réussite de la traduction](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Sortie de la console après la réussite de la traduction")

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio et générer un texte traduit en parole de synthèse, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
