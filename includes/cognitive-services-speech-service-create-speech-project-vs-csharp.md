---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454418"
---
1. Démarrez Visual Studio 2017.
 
1. Vérifiez que la charge de travail **Environnement de bureau .NET** est disponible. Choisissez **Outils** \> **Obtenir des outils et fonctionnalités** dans la barre de menus de Visual Studio pour ouvrir le programme d’installation de Visual Studio. Si cette charge de travail est déjà activée, fermez la boîte de dialogue. 

    Sinon, cochez la case en regard de **Développement .NET Desktop**, puis cliquez sur le bouton **Modifier** dans l’angle inférieur droit de la boîte de dialogue. L’installation de la nouvelle fonctionnalité prendra quelques instants.

    ![Activer un développement de bureau .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Créez une application console Visual C#. Dans la boîte de dialogue **Nouveau projet**, dans le volet gauche, développez **Installé** \> **Visual C#** \> **Windows Desktop**, puis choisissez **Application console (.NET Framework)**. Pour le nom du projet, entrez *helloworld*.

    ![Créer une application console Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Créer une application console Visual C# (.NET Framework)")

1. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gérer les packages NuGet pour la solution")

1. En haut à droite, dans le champ **Source du package**, sélectionnez **nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **helloworld**.

    ![Installer le package NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Installer le package Nuget")

1. Acceptez la licence affichée pour commencer l’installation du package NuGet.

    ![Accepter la licence](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accepter la licence")

    Une fois le package installé, un message de confirmation s’affiche dans la console du Gestionnaire de package.

1. Créez une configuration de plateforme correspondant à l’architecture de votre PC par le biais du Gestionnaire de configurations. Sélectionnez **Générer** > **Gestionnaire de configurations**.

    ![Lancer le Gestionnaire de configurations](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Lancer le Gestionnaire de configurations")

1. Dans la boîte de dialogue **Gestionnaire de configurations**, ajoutez une nouvelle plateforme. Dans la liste déroulante **Plateforme de la solution active**, sélectionnez **Nouveau**.

    ![Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations")

1. Si vous exécutez Windows 64 bits, créez une configuration de plateforme appelée `x64`. Si vous exécutez Windows 32 bits, créez une configuration de plateforme appelée `x86`.

    ![Sur Windows 64 bits, ajouter une nouvelle plateforme nommée « x64 »](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Ajouter une plateforme x64")


