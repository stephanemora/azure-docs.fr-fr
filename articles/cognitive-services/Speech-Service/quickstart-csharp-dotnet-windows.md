---
title: 'Démarrage rapide : reconnaissance vocale dans C# sous .NET Framework sur Windows à l’aide du Kit de développement du logiciel (SDK) Speech de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale dans C# sous .NET Framework sur Windows à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 8b257a6f4c32b4013ac0478d82dc1f7f32675b9b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578158"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>Démarrage rapide : reconnaissance vocale dans C# sous .NET Framework sur Windows à l’aide du kit de développement logiciel (SDK) Speech

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez apprendre à créer une application de console C# pour .NET Framework sur Windows à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech pour transcrire de la parole en texte.
L’application est créée avec le [package NuGet du kit SDK Microsoft Cognitive Services Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Try the speech service for free](get-started.md) (Essayer le service Speech gratuitement).
* Un PC Windows avec un micro opérationnel.
* Visual Studio 2017, Community Edition ou version ultérieure.
* Charge de travail **Développement .NET Desktop** dans Visual Studio. Vous pouvez activer cette charge de travail dans **Outils** \> **Get Tools and Features** (Obtenir des outils et des fonctionnalités).

  ![Activer un développement de bureau .NET](media/sdk/vs-enable-net-desktop-workload.png)

  ![Activer le développement multiplateforme .NET Core](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Dans Visual Studio 2017, créez une application console Visual C#. Dans la boîte de dialogue **Nouveau projet**, dans le volet gauche, développez **Installé** \> **Visual C#** \> **Windows Desktop**, puis sélectionnez **Application console .NET Framework**. Pour le nom du projet, entrez *helloworld*.

    ![Créer une application console Visual C# (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Créer une application console Visual C# (.NET Framework)")

1. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gérer les packages NuGet pour la solution")

1. En haut à droite, dans le champ **Source du package**, sélectionnez **Nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Installer le package NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "Installer le package Nuget")

1. Acceptez la licence affichée.

    ![Accepter la licence](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accepter la licence")

1. La ligne de sortie suivante s’affiche dans la console du gestionnaire de package.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Créer une configuration de plateforme correspondant à l’architecture de votre PC

Dans cette section, vous allez ajouter à la configuration une nouvelle plateforme correspondant à l’architecture de votre processeur.

1. Démarrez le Gestionnaire de configurations. Sélectionnez **Générer** > **Gestionnaire de configurations**.

    ![Lancer le Gestionnaire de configurations](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Lancer le Gestionnaire de configurations")

1. Dans la boîte de dialogue **Gestionnaire de configurations**, ajoutez une nouvelle plateforme. Dans la liste déroulante **Plateforme de la solution active**, sélectionnez **Nouveau**.

    ![Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations")

1. Si vous exécutez Windows 64 bits, créez une configuration de plateforme appelée `x64`. Si vous exécutez Windows 32 bits, créez une configuration de plateforme appelée `x86`. Dans le cadre de cet article, vous allez créer une configuration de plateforme `x64`.

    ![Sur Windows 64 bits, ajouter une nouvelle plateforme nommée « x64 »](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Ajouter une plateforme x64")

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Ouvrez `Program.cs` et remplacez tout le code de ce fichier par ce qui suit.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Générez l’application. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Génération réussie](media/sdk/qs-csharp-dotnet-windows-08-build.png "Génération réussie")

1. Lancez l’application. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Démarrer le débogage de l’application](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Démarrer le débogage de l’application")

1. Une fenêtre de console vous invitant à dire quelque chose (en anglais) s’affiche. Le texte reconnu s’affiche ensuite dans la même fenêtre.

    ![Sortie de la console après la réussite de la reconnaissance](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Sortie de la console après la réussite de la reconnaissance")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Étapes suivantes

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
