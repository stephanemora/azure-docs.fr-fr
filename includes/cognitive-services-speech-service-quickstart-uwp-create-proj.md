---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: fe2978e176b986164ebb01fddbd29481f8a117bd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145427"
---
1. Démarrez Visual Studio 2017.

1. Assurez-vous que la charge de travail **Développement pour la plateforme Windows universelle** est disponible. Choisissez **Outils** > **Obtenir des outils et fonctionnalités** dans la barre de menus de Visual Studio pour ouvrir le programme d’installation de Visual Studio. Si cette charge de travail est déjà activée, fermez la boîte de dialogue.

    ![Capture d’écran du programme d’installation de Visual Studio, avec l’onglet Charges de travail sélectionné](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    Sinon, activez la case à cocher en regard de **Développement multiplateforme .NET** et sélectionnez **Modifier** en bas à droite dans la boîte de dialogue. L’installation de la nouvelle fonctionnalité prend quelques instants.

1. Avec Visual C#, créez une application Windows universelle vide. Dans le menu, choisissez tout d’abord **Fichier** > **Nouveau** > **projet**. Dans la boîte de dialogue **Nouveau projet** du volet gauche, développez **Installés** > **Visual C#**  > **Windows Universel**. Sélectionnez ensuite **Application vide (Windows universelle)** . Pour le nom du projet, entrez *helloworld*.

    ![Capture d’écran de la boîte de dialogue Nouveau projet](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Le kit de développement logiciel (SDK) Speech exige que votre application soit conçue pour Windows 10 Fall Creators Update ou version ultérieure. Dans la fenêtre **Nouveau projet de plateforme Windows universelle** qui s’affiche, choisissez **Windows 10 Fall Creators Update (10.0 ; Build 16299)** comme **Version minimale**. Dans la zone **Version cible**, sélectionnez la même chose ou toute autre version ultérieure, puis cliquez sur **OK**.

    ![Capture d’écran de la fenêtre Nouveau projet de plateforme Windows universelle](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Si vous exécutez Windows 64 bits, vous pouvez basculer votre plateforme de génération vers `x64` par le biais du menu déroulant dans la barre d’outils de Visual Studio. (Windows 64 bits peut exécuter les applications 32 bits, vous pouvez donc laisser ce paramètre défini sur `x86` si vous préférez.)

   ![Capture d’écran de la barre d’outils de Visual Studio, avec l’option x64 mise en surbrillance](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Le SDK Speech prend uniquement en charge les processeurs compatibles Intel. L’architecture ARM n’est actuellement pas prise en charge.

1. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Capture d’écran de l’Explorateur de solutions, avec l’option Gérer les packages NuGet pour la solution mise en surbrillance](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. En haut à droite, dans le champ **Source du package**, sélectionnez **nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Capture d’écran de la boîte de dialogue Gérer les packages de la solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Installer le package NuGet")

1. Acceptez la licence affichée pour commencer l’installation du package NuGet.

    ![Capture d’écran de la boîte de dialogue Acceptation de la licence](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Accepter la licence")

1. La ligne de sortie suivante s’affiche dans la console du gestionnaire de package.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.5.0' to helloworld
   ```

1. Étant donné que l’application utilise le microphone pour la saisie vocale, ajoutez la fonctionnalité **Microphone** au projet. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** pour modifier le manifeste de votre application. Basculez ensuite vers l’onglet **Fonctionnalités**, puis activez la case à cocher de la fonctionnalité **Microphone** et enregistrez vos modifications.

   ![Capture d’écran du manifeste de l’application Visual Studio, avec l’onglet Fonctionnalités sélectionné, et l’option Microphone mise en surbrillance](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
