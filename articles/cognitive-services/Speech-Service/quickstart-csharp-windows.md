---
title: 'Démarrage rapide : reconnaissance vocale à l’aide du Kit de développement logiciel (SDK) Cognitive Services Speech C# pour Windows | Microsoft Docs'
description: Découvrez comment bénéficier de la reconnaissance vocale à l’aide du Kit de développement logiciel (SDK) C# pour le service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 64281215d139731b61365936bc1b837798ad8fbf
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016832"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Démarrage rapide : reconnaissance vocale à l’aide du Kit de développement logiciel (SDK) Cognitive Services Speech C#

Dans cet article, vous allez apprendre à créer une application console C# dans Windows à l’aide du Kit de développement logiciel (SDK) Cognitive Services Speech pour transcrire de la parole en texte.

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Try the speech service for free](get-started.md) (Essayer le service Speech gratuitement).
* Visual Studio 2017, Community Edition ou version ultérieure.
* Charge de travail **Développement .NET Desktop** dans Visual Studio. Vous pouvez activer cette charge de travail dans **Outils** \> **Get Tools and Features** (Obtenir des outils et des fonctionnalités). 

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

1. Dans Visual Studio 2017, créez une application console Visual C#. Dans le volet gauche de la boîte de dialogue **Nouveau projet**, développez **Installé**, puis sélectionnez **Application console (.NET Framework)**. Pour le nom du projet, entrez *CsharpHelloSpeech*.

    ![Créer une application console Visual C# (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Créer une application console Visual C#")

2. Installez et référencez le [package NuGet du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/nuget). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet pour la solution**.

    ![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "Gérer les packages NuGet pour la solution")

3. En haut à droite, dans le champ **Source du package**, sélectionnez **Nuget.org**. Recherchez le package `Microsoft.CognitiveServices.Speech` et installez-le dans le projet **CsharpHelloSpeech**.

    ![Installer le package NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-08-vs-cs-nuget-install.png "Installer le package Nuget")

4. Dans l’écran de licence qui s’affiche, acceptez la licence :

    ![Accepter la licence](media/sdk/speechsdk-09-vs-cs-nuget-license.png "Accepter la licence")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Créer une configuration de plateforme correspondant à l’architecture de votre PC

Dans cette section, vous allez ajouter à la configuration une nouvelle plateforme correspondant à l’architecture de votre processeur.

1. Démarrez le Gestionnaire de configurations. Sélectionnez **Générer** > **Gestionnaire de configurations**.

    ![Lancer le Gestionnaire de configurations](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "Lancer le Gestionnaire de configurations")

2. Dans la boîte de dialogue **Gestionnaire de configurations**, ajoutez une nouvelle plateforme. Dans la liste déroulante **Plateforme de la solution active**, sélectionnez **Nouveau**.

    ![Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "Ajouter une nouvelle plateforme sous la fenêtre du Gestionnaire de configurations")

3. Si vous exécutez Windows 64 bits, créez une configuration de plateforme appelée `x64`. Si vous exécutez Windows 32 bits, créez une configuration de plateforme appelée `x86`. Dans le cadre de cet article, vous allez créer une configuration de plateforme `x64`. 

    ![Sur Windows 64 bits, ajouter une nouvelle plateforme nommée « x64 »](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "Ajouter une plateforme x64")

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Dans le fichier `Program.cs` de votre projet Visual Studio, remplacez le corps de la classe `Program` par le code ci-après. Prenez soin de remplacer la clé et la région d’abonnement par celles que vous avez obtenues pour le service.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. Une fois que vous avez collé le code, la méthode `Main()` doit ressembler à celle figurant dans la capture d’écran suivante :

    ![Méthode Main après le collage du code](media/sdk/speechsdk-17-vs-cs-paste-code.png "Méthode Main finale")

3. La fonction IntelliSense de Visual Studio met en surbrillance les références aux classes du Kit de développement logiciel (SDK) Speech qui n’ont pas pu être résolues. Pour corriger cette erreur, ajoutez l’instruction `using` ci-après au début du code (soit manuellement, soit en utilisant les [actions rapides](https://docs.microsoft.com/visualstudio/ide/quick-actions) de Visual Studio).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![Utiliser l’action rapide pour ajouter l’instruction using manquante](media/sdk/speechsdk-18-vs-cs-add-using.png "Résoudre les problèmes IntelliSense")

4. Assurez-vous que les éléments mis en surbrillance par IntelliSense sont résolus, puis enregistrez les modifications du projet.

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

1. Générez l’application. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution**. Le code doit maintenant se compiler sans erreurs :

    ![Génération réussie](media/sdk/speechsdk-20-vs-cs-build.png "Génération réussie")

2. Lancez l’application. Dans la barre de menus, sélectionnez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**. 

    ![Démarrer le débogage de l’application](media/sdk/speechsdk-21-vs-cs-f5.png "Démarrer le débogage de l’application")

3. Une fenêtre de console vous invitant à dire quelque chose (en anglais) s’affiche.
Le résultat de la reconnaissance s’affiche à l’écran.

    ![Sortie de la console après la réussite de la reconnaissance](media/sdk/speechsdk-22-cs-vs-console-output.png "Sortie de la console après la réussite de la reconnaissance")

## <a name="download-code"></a>Télécharger le code

Pour obtenir le dernier ensemble d’exemples, consultez le [référentiel GitHub d’exemples pour le Kit de développement logiciel (SDK) Cognitive Services Speech](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Étapes suivantes

- [Traduction vocale](how-to-translate-speech.md)
- [Modèles vocaux personnalisés](how-to-customize-speech-models.md)
