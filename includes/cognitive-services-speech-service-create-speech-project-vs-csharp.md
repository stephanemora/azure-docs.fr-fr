---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362499"
---
1. Ouvrez Visual Studio 2019.

1. Dans la fenêtre Démarrer, sélectionnez **Créer un projet**. 

1. Sélectionnez **Application console (.NET Framework)** , puis **Suivant**.

1. Dans **Nom du projet**, entrez `helloworld`, puis sélectionnez **Créer**.

1. Dans la barre de menus de Visual Studio, sélectionnez **Outils** > **Obtenir les outils et fonctionnalités** et vérifiez si la charge de travail **Développement .NET Desktop** est disponible. Si cette charge de travail n’a pas été installée, cochez la case et sélectionnez **Modifier** pour démarrer l’installation. Le téléchargement et l’installation peuvent prendre quelques minutes.

   Si la case à cocher à côté de **Développement .NET Desktop** est sélectionnée, vous pouvez fermer la boîte de dialogue.

   ![Activer un développement de bureau .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

L’étape suivante consiste à installer le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) afin de pouvoir le référencer dans le code.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `helloworld`, puis sélectionnez **Gérer les packages NuGet** pour afficher le gestionnaire de package NuGet.

   ![Gestionnaire de package NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Dans l’angle supérieur droit, recherchez la zone de liste déroulante **Source du package** et vérifiez que **nuget.org** est sélectionné.

1. Dans l’angle supérieur gauche, sélectionnez **Parcourir.**

1. Dans la zone de recherche, tapez package `Microsoft.CognitiveServices.Speech` et appuyez sur Entrée.

1. Sélectionnez `Microsoft.CognitiveServices.Speech`, puis **Installer** pour installer la dernière version stable.

   ![Installer le package NuGet Microsoft.CognitiveServices.Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Acceptez tous les contrats et licences pour démarrer l’installation.

   Une fois le package installé, un message de confirmation s’affiche dans la fenêtre **Console du Gestionnaire de package**.

Maintenant, pour générer et exécuter l’application console, créez une configuration de plateforme correspondant à l’architecture de votre ordinateur.

1. Dans la barre de menus, sélectionnez **Générer** > **Gestionnaire de configurations**. La boîte de dialogue **Gestionnaire de configurations** s’affiche.

   ![Boîte de dialogue Gestionnaire de configurations](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Dans la zone de liste déroulante **Plateforme de la solution active**, sélectionnez **Nouveau**. La boîte de dialogue **Nouvelle plateforme de solution** s’affiche.

1. Dans la zone de liste déroulante **Tapez ou sélectionnez la nouvelle plateforme** :
   - Si vous exécutez Windows 64 bits, sélectionnez **x64**.
   - Si vous exécutez Windows 32 bits, sélectionnez **x86**.

1. Sélectionnez **OK**, puis **Fermer**.
