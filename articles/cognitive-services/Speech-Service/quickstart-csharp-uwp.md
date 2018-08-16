---
title: 'Démarrage rapide : reconnaissance vocale dans C# dans une application UWP, à l’aide du kit de développement logiciel (SDK) Speech de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale dans une application UWP à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: d1245b07ac0de680c13542b9af86b25bdf517c21
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576132"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Démarrage rapide : reconnaissance vocale dans une application UWP à l’aide du kit de développement logiciel (SDK) Speech

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous allez apprendre à créer une application plateforme Windows universelle (UWP9 à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech pour transcrire de la parole en texte.
L’application est créée avec le [package NuGet du kit SDK Microsoft Cognitive Services Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017.

> [!NOTE]
> La plateforme Windows universelle vous permet de développer des applications qui s’exécutent sur n’importe quel appareil prenant en charge Windows 10, notamment des PC, Xbox, Surface Hub et d’autres appareils. Les applications utilisant le kit de développement logiciel (SDK) Speech ne passent pas encore le Kit de certification des applications Windows (WACK). Il est possible de charger une version test de votre application, mais elle ne peut actuellement pas être envoyée à Windows Store.

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Try the speech service for free](get-started.md) (Essayer le service Speech gratuitement).
* Un PC Windows (Windows 10 Fall Creators Update ou une version ultérieure) avec un micro qui fonctionne.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition ou version ultérieure.
* La charge de travail de **développement de plateforme Windows universelle** dans Visual Studio. Vous pouvez l’activer dans **Outils** \> **Obtenir des outils et des fonctionnalités**.

  ![Activer le développement pour la plateforme Windows universelle](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Dans Visual Studio 2017, créez une application universelle Visual C# Windows vide. Dans la boîte de dialogue **Nouveau projet**, dans le volet gauche, développez **Installé** \> **Visual C#** \> **Windows universel**, puis sélectionnez **Application vide (Windows universel)**. Pour le nom du projet, entrez *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Dans la fenêtre **Nouveau projet de plateforme Windows universelle** qui s’affiche, choisissez **Windows 10 Fall Creators Update (10.0 ; build 16299)** comme **version minimale** et cette version ou toute version ultérieure comme **version cible**, puis cliquez sur **OK**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Si vous effectuez une exécution sur une installation Windows 64 bits, vous pouvez basculer votre plateforme de génération sur `x64`.

   ![Passer la plateforme de génération à x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > À ce stade, le kit de développement logiciel (SDK) Speech prend en charge les processeurs compatibles Intel, mais pas ARM.

1. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. En haut à droite, dans le champ **Source du package**, sélectionnez **Nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Installer le package NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "Installer le package Nuget")

1. Acceptez la licence affichée.

    ![Accepter la licence](media/sdk/qs-csharp-uwp-06-nuget-license.png "Accepter la licence")

1. La ligne de sortie suivante s’affiche dans la console du gestionnaire de package.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** pour modifier votre manifeste d’application.
   Sélectionnez l’onglet **Fonctionnalités**, puis la case à cocher pour la fonctionnalité **Micro**, et enregistrez vos modifications.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Modifiez l’interface utilisateur de votre application en double-cliquant sur `MainPage.xaml` dans l’Explorateur de solutions. 

    Dans la vue XAML du concepteur, insérez l’extrait de code XAML suivant dans la balise de grille (entre `<Grid>` et `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Modifier le code-behind XAML en double-cliquant sur `MainPage.xaml.cs` dans l’Explorateur de solutions (elles sont groupées sous l’élément `MainPage.xaml`).
   Remplacez tout le code de ce fichier par les lignes de code suivantes.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Dans le gestionnaire `SpeechRecognitionFromMicrophone_ButtonClicked`, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Dans le gestionnaire `SpeechRecognitionFromMicrophone_ButtonClicked`, remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez toutes les modifications dans le projet.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Générez l’application. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs.

    ![Génération réussie](media/sdk/qs-csharp-uwp-08-build.png "Génération réussie")

1. Lancez l’application. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Démarrer le débogage de l’application](media/sdk/qs-csharp-uwp-09-start-debugging.png "Démarrer le débogage de l’application")

1. Une fenêtre d’interface graphique utilisateur s’affiche. Cliquez d’abord sur le bouton **Activer le micro** et accusez réception de la demande d’autorisation qui s’affiche.

    ![Démarrer le débogage de l’application](media/sdk/qs-csharp-uwp-10-access-prompt.png "Démarrer le débogage de l’application")

1. Cliquez sur la **reconnaissance vocale avec entrée par micro** et prononcez une phrase courte dans le micro de votre appareil. Le texte reconnu s’affiche dans la fenêtre.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Étapes suivantes

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
