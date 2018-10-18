---
title: 'Démarrage rapide : Reconnaissance vocale en C# dans une application UWP, à l’aide du kit SDK Speech de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale dans une application UWP en utilisant le kit SDK Speech de Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: d9a90869e060d2f8f1a1c522a4528e74841caada
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339612"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale dans une application UWP, à l’aide du kit SDK Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application Universal Windows Platform (UWP) C# en utilisant le [kit SDK Speech](speech-sdk.md) de Cognitive Services. Vous transcrivez la reconnaissance vocale en temps réel à partir du microphone de votre appareil. L’application est créée avec le [package NuGet du kit SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017 (toute édition).

> [!NOTE]
> La plateforme Windows universelle vous permet de développer des applications qui s’exécutent sur n’importe quel appareil prenant en charge Windows 10, notamment des PC, Xbox, Surface Hub et d’autres appareils.

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Consultez [Essayer le service Speech gratuitement](get-started.md) pour plus d’informations.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Démarrez Visual Studio 2017.

1. Assurez-vous que la charge de travail **Développement pour la plateforme Windows universelle** est disponible. Choisissez **Outils** > **Obtenir des outils et fonctionnalités** dans la barre de menus de Visual Studio pour ouvrir le programme d’installation de Visual Studio. Si cette charge de travail est déjà activée, fermez la boîte de dialogue. 

    ![Capture d’écran du programme d’installation de Visual Studio, avec l’onglet Charges de travail sélectionné](media/sdk/vs-enable-uwp-workload.png)

    Sinon, activez la case à cocher en regard de **Développement multiplateforme .NET** et sélectionnez **Modifier** en bas à droite dans la boîte de dialogue. L’installation de la nouvelle fonctionnalité prend quelques instants.

1. Avec Visual C#, créez une application Windows universelle vide. Dans le menu, choisissez tout d’abord **Fichier** > **Nouveau** > **projet**. Dans la boîte de dialogue **Nouveau projet** du volet gauche, développez **Installés** > **Visual C#** > **Windows Universel**. Sélectionnez ensuite **Application vide (Windows universelle)**. Pour le nom du projet, entrez *helloworld*.

    ![Capture d’écran de la boîte de dialogue Nouveau projet](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Pour utiliser le kit SDK Speech, votre application doit être générée pour Windows 10 Fall Creators Update, ou version ultérieure. Dans la fenêtre **Nouveau projet de plateforme Windows universelle** qui s’affiche, choisissez **Windows 10 Fall Creators Update (10.0 ; Build 16299)** comme **Version minimale**. Dans la zone **Version cible**, sélectionnez la même chose ou toute autre version ultérieure, puis cliquez sur **OK**.

    ![Capture d’écran de la fenêtre Nouveau projet de plateforme Windows universelle](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Si vous exécutez Windows 64 bits, vous pouvez basculer votre plateforme de génération vers `x64` par le biais du menu déroulant dans la barre d’outils de Visual Studio. (Windows 64 bits peut exécuter les applications 32 bits, vous pouvez donc laisser ce paramètre défini sur `x86` si vous préférez.)

   ![Capture d’écran de la barre d’outils de Visual Studio, avec l’option x64 mise en surbrillance](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Le kit SDK Speech prend en charge les processeurs compatibles Intel uniquement. L’architecture ARM n’est actuellement pas prise en charge.

1. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Capture d’écran de l’Explorateur de solutions, avec l’option Gérer les packages NuGet pour la solution mise en surbrillance](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. En haut à droite, dans le champ **Source du package**, sélectionnez **nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Capture d’écran de la boîte de dialogue Gérer les packages de la solution](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Installer le package NuGet")

1. Acceptez la licence affichée pour commencer l’installation du package NuGet.

    ![Capture d’écran de la boîte de dialogue Acceptation de la licence](media/sdk/qs-csharp-uwp-06-nuget-license.png "Accepter la licence")

1. La ligne de sortie suivante s’affiche dans la console du gestionnaire de package.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.1' to helloworld
   ```

1. Étant donné que l’application utilise le microphone pour la saisie vocale, ajoutez la fonctionnalité **Microphone** au projet. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** pour modifier le manifeste de votre application. Basculez ensuite vers l’onglet **Fonctionnalités**, puis activez la case à cocher de la fonctionnalité **Microphone** et enregistrez vos modifications.

   ![Capture d’écran du manifeste de l’application Visual Studio, avec l’onglet Fonctionnalités sélectionné, et l’option Microphone mise en surbrillance](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Ajouter un exemple de code

1. L’interface utilisateur de l’application est définie à l’aide de XAML. Ouvrez `MainPage.xaml` dans l’Explorateur de solutions. Dans la vue XAML du concepteur, insérez l’extrait de code XAML suivant dans la balise de grille (entre `<Grid>` et `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Ouvrez le fichier source code-behind `MainPage.xaml.cs` (vous le trouvez regroupé sous `MainPage.xaml`). Remplacez tout le code de ce fichier par ce qui suit.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Dans le gestionnaire `SpeechRecognitionFromMicrophone_ButtonClicked` de ce fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Dans le gestionnaire `SpeechRecognitionFromMicrophone_ButtonClicked`, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez toutes les modifications dans le projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-csharp-uwp-08-build.png "Build réussie")

1. Lancez l’application. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-csharp-uwp-09-start-debugging.png "Démarrer l’application en débogage")

1. Une fenêtre s’affiche. Sélectionnez **Enable Microphone** (Activer le microphone) et confirmez la demande d’autorisation qui s’affiche.

    ![Capture d’écran de la demande d’autorisation](media/sdk/qs-csharp-uwp-10-access-prompt.png "Démarrer l’application en débogage")

1. Sélectionnez **Speech recognition with microphone input** (Reconnaissance vocale avec entrée par micro) et prononcez une phrase ou quelques mots en anglais dans le micro de votre appareil. Votre production orale est transmise au service Speech, et transcrite en texte qui apparaît dans la fenêtre.

    ![Capture d’écran de l’interface utilisateur de la reconnaissance vocale](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Effectuer une reconnaissance des intentions vocales à l’aide du kit SDK Speech pour C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Voir aussi

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
