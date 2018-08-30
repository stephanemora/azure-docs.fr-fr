---
title: 'Démarrage rapide : reconnaissance vocale dans C++ sur Windows Desktop à l’aide du Kit de développement logiciel (SDK) Speech de Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Découvrez comment effectuer une reconnaissance vocale dans C++ sur Windows Desktop à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 9fae855de2a746084f4775f194e04c6dbe09f684
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127289"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Démarrage rapide : reconnaissance vocale dans C++ sur Windows Desktop à l’aide du kit de développement logiciel (SDK) Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Nous expliquons comment créer une application console C++ pour Windows Desktop qui utilise le kit SDK de reconnaissance vocale.
L’application est basée sur le [package NuGet du kit SDK Microsoft Cognitive Services Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Try the speech service for free](get-started.md) (Essayer le service Speech gratuitement).
* Un PC Windows avec un micro opérationnel.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition ou version ultérieure.
* La charge de travail **Développement Desktop avec C++** dans Visual Studio et le composant **Gestionnaire de package NuGet** dans Visual Studio.
  Vous pouvez activer ces deux éléments dans **Outils** \> **Obtenir des outils et des fonctionnalités**, dans les onglets **Charges de travail** et **Composants individuels**, respectivement :

  ![Activer la charge de travail Développement Desktop avec C++](media/sdk/vs-enable-cpp-workload.png)

  ![Activer le gestionnaire de package NuGet dans Visual Studio ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

Dans Visual Studio 2017, créez une application console Windows Desktop Visual C++. Dans la boîte de dialogue **Nouveau projet**, dans le volet gauche, développez **Installé** \> **Visual C++** \> **Windows Desktop**, puis sélectionnez **Application console Windows**. Pour le nom du projet, entrez *helloworld*.

![Créer une application console Windows Desktop Visual C++](media/sdk/qs-cpp-windows-01-new-console-app.png)

Si vous effectuez une exécution sur une installation Windows 64 bits, vous pouvez éventuellement passer votre plateforme de génération à `x64` :

![Passer la plateforme de génération à x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Installer et référencer le package NuGet du kit SDK de reconnaissance vocale

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution**.

![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

En haut à droite, dans le champ **Source du package**, choisissez « Nuget.org ».
Sous l’onglet **Parcourir**, recherchez le package « Microsoft.CognitiveServices.Speech », sélectionnez-le, puis cochez les cases **Projet** et **helloworld** à droite. Sélectionnez ensuite **Installer** pour l’installer dans le projet helloworld.

> [!NOTE]
> La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `0.6.0`.

![Installer le package NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

Dans l’écran de licence qui s’affiche, acceptez la licence :

![Accepter la licence](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Remplacez votre code de démarrage par défaut par le suivant :

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Générez l’application. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs :

   ![Build réussie](media/sdk/qs-cpp-windows-06-build.png)

1. Lancez l’application. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

   ![Lancer l’application en débogage](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Une fenêtre de console vous invitant à dire quelque chose (en anglais) s’affiche.
   Le résultat de la reconnaissance s’affiche sur l’écran.

   ![Sortie de la console après une reconnaissance réussie](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/cpp-windows`.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez nos exemples](speech-sdk.md#get-the-samples)
