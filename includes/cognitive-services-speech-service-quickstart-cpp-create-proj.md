---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: af61d975b540787b9b54d9fdea66773c10aeb6ac
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020780"
---
1. Démarrez Visual Studio 2017.

1. Assurez-vous que la charge de travail **Développement Desktop en C++** est disponible. Choisissez **Outils** > **Obtenir des outils et fonctionnalités** dans la barre de menus de Visual Studio pour ouvrir le programme d’installation de Visual Studio. Si cette charge de travail est déjà activée, passez à l’étape suivante.

    ![Capture d’écran de l’onglet Charges de travail dans Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    Sinon, cochez la case en regard de **Développement Desktop en C++**.

1. Assurez-vous que le composant **Gestionnaire de package NuGet** est disponible. Basculez vers l’onglet **Composants individuels** de la boîte de dialogue du programme d’installation de Visual Studio, puis sélectionnez **Gestionnaire de package NuGet** s’il n’est pas déjà activé.

      ![Capture d’écran de l’onglet Composants individuels dans Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Si vous deviez activer la charge de travail C++ ou NuGet, sélectionnez **Modifier** (en bas à droite dans la boîte de dialogue). L’installation des nouvelles fonctionnalités prend quelques instants. Si ces deux fonctionnalités étaient déjà activées, fermez la boîte de dialogue.

1. Créez une application console Windows : Windows Desktop avec Visual C++. Dans le menu, choisissez tout d’abord **Fichier** > **Nouveau** > **projet**. Dans la boîte de dialogue **Nouveau projet** du volet gauche, développez **Installés** > **Visual C++** > **Windows Desktop**. Ensuite, sélectionnez **Application console Windows**. Pour le nom du projet, entrez *helloworld*.

    ![Capture d’écran de la boîte de dialogue Nouveau projet](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Si vous exécutez Windows 64 bits, vous pouvez basculer votre plateforme de génération vers `x64` en utilisant le menu déroulant dans la barre d’outils de Visual Studio. (Les versions 64 bits de Windows peuvent exécuter des applications 32 bits, cela n’est donc pas obligatoire.)

    ![Capture d’écran de la barre d’outils de Visual Studio, avec l’option x64 mise en surbrillance](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis choisissez **Gérer les packages NuGet pour la solution**.

    ![Capture d’écran de l’Explorateur de solutions, avec l’option Gérer les packages NuGet pour la solution mise en surbrillance](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. En haut à droite, dans le champ **Source du package**, sélectionnez **nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Capture d’écran de la boîte de dialogue Gérer les packages de la solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.5.0`.

1. Acceptez la licence affichée pour commencer l’installation du package NuGet.

    ![Capture d’écran de la boîte de dialogue Acceptation de la licence](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

Une fois le package installé, un message de confirmation s’affiche dans la console du Gestionnaire de package.
