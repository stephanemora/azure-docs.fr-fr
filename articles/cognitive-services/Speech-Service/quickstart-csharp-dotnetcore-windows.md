---
title: 'Démarrage rapide : Effectuer une reconnaissance vocale en C# sous .NET Core sur Windows à l’aide du kit SDK Speech de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale en C# sous .NET Core sur Windows à l’aide du kit SDK Speech de Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 04cdfd487036e79eccbb4fb879902a12e077d104
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341648"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Démarrage rapide : Effectuer une reconnaissance vocale en C# sous .NET Core sur Windows à l’aide du kit SDK Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console C# pour .NET Core sur Windows en utilisant le [kit SDK Speech](speech-sdk.md) de Cognitive Services. Vous transcrivez la reconnaissance vocale en temps réel à partir du microphone de votre PC. L’application est créée avec le [package NuGet du kit SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017 (toute édition).

> [!NOTE]
> .NET Core est une plateforme .NET à vocation multiplateforme, open source, qui implémente la spécification [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Consultez [Essayer le service Speech gratuitement](get-started.md) pour plus d’informations.


## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Démarrez Visual Studio 2017.

1. Assurez-vous que la charge de travail **Développement multiplateforme .NET Core** est disponible. Choisissez **Outils** > **Obtenir des outils et fonctionnalités** dans la barre de menus de Visual Studio pour ouvrir le programme d’installation de Visual Studio. Si cette charge de travail est déjà activée, fermez la boîte de dialogue.

    ![Capture d’écran du programme d’installation de Visual Studio, avec l’onglet Charges de travail sélectionné](media/sdk/vs-enable-net-core-workload.png)

    Sinon, activez la case à cocher en regard de **Développement multiplateforme .NET Core** et sélectionnez **Modifier** en bas à droite dans la boîte de dialogue. L’installation de la nouvelle fonctionnalité prend quelques instants.

1. Créez une application console .NET Core avec Visual C#. Dans le volet gauche de la boîte de dialogue **Nouveau projet**, développez **Installés** > **Visual C#** > **.NET Core**. Ensuite, sélectionnez **Application console (.NET Core)**. Pour le nom du projet, entrez *helloworld*.

    ![Capture d’écran de la boîte de dialogue Nouveau projet](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Créer une application console Visual C# (.NET Core)")

1. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Capture d’écran de l’Explorateur de solutions, avec l’option Gérer les packages NuGet pour la solution mise en surbrillance](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gérer les packages NuGet pour la solution")

1. En haut à droite, dans le champ **Source du package**, sélectionnez **nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Capture d’écran de la boîte de dialogue Gérer les packages de la solution](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installer le package NuGet")

1. Acceptez la licence affichée pour commencer l’installation du package NuGet.

    ![Capture d’écran de la boîte de dialogue Acceptation de la licence](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accepter la licence")

Une fois le package installé, un message de confirmation s’affiche dans la console du Gestionnaire de package.


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

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Effectuer une reconnaissance des intentions vocales à l’aide du kit SDK Speech pour C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Voir aussi

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
