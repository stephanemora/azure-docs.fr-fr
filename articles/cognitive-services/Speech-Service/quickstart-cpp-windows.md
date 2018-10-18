---
title: 'Démarrage rapide : Reconnaissance vocale en C++ sur Windows à l’aide du kit SDK Speech de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale en C++ sur Windows Desktop à l’aide du kit SDK Speech de Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: Speech
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 2adad78760c1d12da688106c45e86c91a8b13f91
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339353"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en C++ sur Windows à l’aide du kit SDK Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console C++ pour Windows. Vous utilisez le [kit SDK Speech](speech-sdk.md) de Cognitive Services pour transcrire la reconnaissance vocale en temps réel à partir du microphone de votre PC. L’application est créée avec le [package NuGet du kit SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017 (toute édition).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Consultez [Essayer le service Speech gratuitement](get-started.md) pour plus d’informations.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Démarrez Visual Studio 2017.

1. Assurez-vous que la charge de travail **Développement Desktop en C++** est disponible. Choisissez **Outils** > **Obtenir des outils et fonctionnalités** dans la barre de menus de Visual Studio pour ouvrir le programme d’installation de Visual Studio. Si cette charge de travail est déjà activée, passez à l’étape suivante. 

    ![Capture d’écran de l’onglet Charges de travail dans Visual Studio](media/sdk/vs-enable-cpp-workload.png)

    Sinon, cochez la case en regard de **Développement Desktop en C++**. 

1. Assurez-vous que le composant **Gestionnaire de package NuGet** est disponible. Basculez vers l’onglet **Composants individuels** de la boîte de dialogue du programme d’installation de Visual Studio, puis sélectionnez **Gestionnaire de package NuGet** s’il n’est pas déjà activé.

      ![Capture d’écran de l’onglet Composants individuels dans Visual Studio](media/sdk/vs-enable-nuget-package-manager.png)

1. Si vous deviez activer la charge de travail C++ ou NuGet, sélectionnez **Modifier** (en bas à droite dans la boîte de dialogue). L’installation des nouvelles fonctionnalités prend quelques instants. Si ces deux fonctionnalités étaient déjà activées, fermez la boîte de dialogue.

1. Créez une application console Windows : Windows Desktop avec Visual C++. Dans le menu, choisissez tout d’abord **Fichier** > **Nouveau** > **projet**. Dans la boîte de dialogue **Nouveau projet** du volet gauche, développez **Installés** > **Visual C++** > **Windows Desktop**. Ensuite, sélectionnez **Application console Windows**. Pour le nom du projet, entrez *helloworld*.

    ![Capture d’écran de la boîte de dialogue Nouveau projet](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Si vous exécutez Windows 64 bits, vous pouvez basculer votre plateforme de génération vers `x64` en utilisant le menu déroulant dans la barre d’outils de Visual Studio. (Les versions 64 bits de Windows peuvent exécuter des applications 32 bits, cela n’est donc pas obligatoire.)

    ![Capture d’écran de la barre d’outils de Visual Studio, avec l’option x64 mise en surbrillance](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis choisissez **Gérer les packages NuGet pour la solution**.

    ![Capture d’écran de l’Explorateur de solutions, avec l’option Gérer les packages NuGet pour la solution mise en surbrillance](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. En haut à droite, dans le champ **Source du package**, sélectionnez **nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Capture d’écran de la boîte de dialogue Gérer les packages de la solution](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.0.1`.

1. Acceptez la licence affichée pour commencer l’installation du package NuGet.

    ![Capture d’écran de la boîte de dialogue Acceptation de la licence](media/sdk/qs-cpp-windows-05-nuget-license.png)

Une fois le package installé, un message de confirmation s’affiche dans la console du Gestionnaire de package.

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source *helloworld.cpp*. Remplacez tout le code sous l’instruction include initiale (`#include "stdafx.h"` ou `#include "pch.h"`) par le code suivant :

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, choisissez **Générer** > **Générer la solution**. Le code doit se compiler sans erreur.

   ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution mise en surbrillance](media/sdk/qs-cpp-windows-06-build.png)

1. Lancez l’application. Dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

   ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage mise en surbrillance](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Une fenêtre de console s’affiche vous invitant à dire quelque chose. Prononcez une phrase ou quelques mots en anglais. Votre production orale est transmise au service Speech, et transcrite en texte qui apparaît dans la même fenêtre.

   ![Capture d’écran de la sortie de la console après une reconnaissance réussie](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/cpp-windows`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Effectuer une reconnaissance des intentions vocales à l’aide du kit SDK Speech pour C++](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Voir aussi

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
