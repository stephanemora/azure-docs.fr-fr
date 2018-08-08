---
title: 'Démarrage rapide : reconnaissance vocale dans C# sous .NET Core sur Windows à l’aide du Kit de développement logiciel (SDK) Speech de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale dans C# sous .NET Core sur Windows à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 7790920b6553ba0e2738d693710bfc3a1d3b4f89
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325099"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Démarrage rapide : reconnaissance vocale dans C# sous .NET Core sur Windows à l’aide du kit de développement logiciel (SDK) Speech

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez apprendre à créer une application de console C# pour .NET Core sur Windows à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech pour transcrire de la parole en texte.
L’application est créée avec le [package NuGet du kit SDK Microsoft Cognitive Services Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017.

> [!NOTE]
> .NET Core est une multiplateforme open source, .NET implémentant la spécification [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Try the speech service for free](get-started.md) (Essayer le service Speech gratuitement).
* Un PC Windows avec un micro opérationnel.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition ou version ultérieure.
* Charge de travail **Développement de multiplateforme .NET Core** dans Visual Studio. Vous pouvez activer cette charge de travail dans **Outils** \> **Get Tools and Features** (Obtenir des outils et des fonctionnalités).

  ![Activer le développement multiplateforme .NET Core](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Dans Visual Studio 2017, créez une application console .NET Core Visual C#. Dans le volet gauche de la boîte de dialogue **Nouveau projet**, développez **Installé** \> **Visual C#** \> **.NET Core**, puis sélectionnez **Application console (.NET Core)**. Pour le nom du projet, entrez *helloworld*.

    ![Créer une application console Visual C# (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Créer une application console Visual C# (.NET Core)")

1. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gérer les packages NuGet pour la solution")

1. En haut à droite, dans le champ **Source du package**, sélectionnez **Nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Installer le package NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "Installer le package Nuget")

1. Acceptez la licence dans la boîte de dialogue qui s’affiche.

    ![Accepter la licence](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accepter la licence")

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Ouvrez `Program.cs` dans votre projet Visual Studio et remplacez tout le code dans ce fichier par les lignes de code suivantes.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Générez l’application. Dans la barre de menus, choisissez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Génération réussie](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Génération réussie")

1. Lancez l’application. Dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Démarrer le débogage de l’application](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Démarrer le débogage de l’application")

1. Une fenêtre de console vous invitant à dire quelque chose (en anglais) s’affiche. Le texte reconnu s’affiche ensuite dans la même fenêtre.

    ![Sortie de la console après la réussite de la reconnaissance](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Sortie de la console après la réussite de la reconnaissance")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Étapes suivantes

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
