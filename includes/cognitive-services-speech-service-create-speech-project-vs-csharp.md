---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177356"
---
1. Démarrez Visual Studio 2017.

1. Sur la barre de menus de Visual Studio, sélectionnez **Outils > Obtenir les outils** et vérifiez que la charge de travail **Développement .NET Desktop** est disponible. Si la charge de travail n’a pas été installée, cochez la case et cliquez sur **Modifier** pour démarrer l’installation. Le téléchargement et l’installation peuvent prendre quelques minutes.

   Si la case à cocher à côté de **Développement .NET Desktop** est sélectionnée, vous pouvez fermer la boîte de dialogue.

   ![Activer un développement de bureau .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Maintenant, créons un projet. Dans la barre de menus, sélectionnez **Fichier > Nouveau > Projet**. Quand la boîte de dialogue apparaît, dans le panneau de gauche, développez ces sections **Installé > Visual C# > Windows Desktop** et sélectionnez **Application console (.NET Framework)** . Nommez ce projet *helloworld*.

    ![Créer une application console Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Créer une application console Visual C# (.NET Framework)")

1. Maintenant que le projet est configuré, nous devons installer le [package NuGet du kit SDK Speech](https://aka.ms/csspeech/nuget) et le référencer dans notre code. Recherchez l’Explorateur de solutions et cliquez avec le bouton droit sur helloworld. Dans le menu, sélectionnez **Gérer les packages NuGet...** .

   ![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gérer les packages NuGet pour la solution")

1. En haut à droite du Gestionnaire de package NuGet, recherchez la liste déroulante **Source du package** et vérifiez que **nuget.org** est sélectionné. Ensuite, sélectionnez **Parcourir**, recherchez le package `Microsoft.CognitiveServices.Speech` et installez la dernière version stable.

   ![Installer le package NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Installer le package Nuget")

1. Acceptez tous les contrats et licences pour démarrer l’installation.

   ![Accepter la licence](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accepter la licence")

    Une fois le package installé, un message de confirmation s’affiche dans la console du Gestionnaire de package.

1. L’étape suivante consiste à créer une configuration de plateforme qui correspond à l’architecture de l’ordinateur que vous utilisez pour générer et exécuter l’application console. Dans la barre de menus, sélectionnez **Générer** > **Gestionnaire de configurations...** .

    ![Lancer le Gestionnaire de configurations](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Lancer le Gestionnaire de configurations")

1. Dans la boîte de dialogue **Gestionnaire de configurations**, recherchez la liste déroulante **Plateforme de la solution active** et sélectionnez **Nouvelle**.

    ![Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations")

1. Si vous exécutez Windows 64 bits, lorsque vous y êtes invité, **entrez ou sélectionnez la nouvelle plateforme**, `x64`. Si vous exécutez Windows 32 bits, sélectionnez `x86`. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Sur Windows 64 bits, ajouter une nouvelle plateforme nommée « x64 »](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Ajouter une plateforme x64")
