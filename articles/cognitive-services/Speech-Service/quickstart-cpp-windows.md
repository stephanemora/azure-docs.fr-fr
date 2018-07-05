---
title: Démarrage rapide du kit SDK de reconnaissance vocale pour C++ et Windows | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Obtenez des informations et des exemples de code pour vous aider à commencer rapidement à utiliser le kit SDK de reconnaissance vocale avec Windows et C++ dans Cognitive Services.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049228"
---
# <a name="quickstart-for-c-and-windows"></a>Démarrage rapide pour C++ et Windows

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `0.4.0`.

Nous expliquons comment créer une application console C++ pour Windows Desktop qui utilise le kit SDK de reconnaissance vocale.
L’application est basée sur le [package NuGet du kit SDK Microsoft Cognitive Services](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) et Microsoft Visual Studio 2017.

> [!NOTE]
> Si vous recherchez un guide de démarrage rapide pour C++ et Linux, rendez-vous [ici](quickstart-cpp-linux.md).<br>
> Si vous recherchez un guide de démarrage rapide pour C# et Windows, rendez-vous [ici](quickstart-csharp-windows.md).

> [!NOTE]
> Ce guide de démarrage rapide nécessite un PC avec un microphone qui fonctionne.<br>
> Pour voir un exemple qui procède à la reconnaissance vocale d’un fichier audio spécifique, consultez l’[exemple](speech-to-text-sample.md#speech-recognition-from-a-file).

> [!NOTE]
> Vérifiez que votre installation Visual Studio inclut la charge de travail **Développement Desktop en C++**.
> Si vous avez des doutes, utilisez les étapes suivantes pour vérifier et corriger : Dans Visual Studio 2017, sélectionnez **Outils** \> **Obtenir des outils et des fonctionnalités**, confirmez l’invite du contrôle de compte d’utilisateur en choisissant **Oui**.
> Sous l’onglet **Charges de travail**, si aucune case n’est cochée en regard de **Développement Desktop en C++**, définissez-la, puis cliquez sur **Modifier** pour enregistrer les modifications apportées.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Création d’un projet d’application console vide

Dans Visual Studio 2017, créez une application console Windows Desktop Visual C++ avec le nom « CppHelloSpeech » :

![Créer une application console Windows Desktop Visual C++](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Si vous effectuez une exécution sur une installation Windows 64 bits, vous pouvez éventuellement passer votre plateforme de génération à `x64` :

![Passer la plateforme de génération à x64](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Installer et référencer le package NuGet du kit SDK de reconnaissance vocale

> [!NOTE]
> Vérifiez que le gestionnaire de package NuGet est activé pour votre installation Visual Studio 2017.
> Dans Visual Studio 2017, sélectionnez **Outils** \> **Obtenir des outils et des fonctionnalités**, confirmez l’invite du contrôle de compte d’utilisateur en choisissant **Oui**. Sélectionnez ensuite l’onglet **Composants individuels**, puis recherchez **Gestionnaire de package NuGet** sous **Outils de code**.
> Si la case à sa gauche n’est pas cochée, veillez à la définir, puis cliquez sur **Modifier** pour enregistrer les modifications apportées.
>
> ![Activer le gestionnaire de package NuGet dans Visual Studio ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution**.

![Cliquer avec le bouton droit sur Gérer les packages NuGet pour la solution](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

En haut à droite, dans le champ **Source du package**, choisissez « Nuget.org ».
Sous l’onglet **Parcourir**, recherchez le package « Microsoft.CognitiveServices.Speech », sélectionnez-le, puis cochez les cases **Projet** et **CppHelloSpeech** à droite. Sélectionnez ensuite **Installer** pour l’installer dans le projet CppHelloSpeech.

![Installer le package NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

Dans l’écran de licence qui s’affiche, acceptez la licence :

![Accepter la licence](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

Remplacez votre code de démarrage par défaut par le suivant :

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Remplacez la clé d’abonnement par celle que vous avez obtenue. <br>
> Remplacez la région par votre région de [l’API REST du service de reconnaissance vocale](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis) ; par exemple, remplacez-la par « westus ».

![Ajouter votre clé d’abonnement](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

Le code doit maintenant se compiler sans erreurs :

![Build réussie](media/sdk/speechsdk-16-vs-cpp-build.png)

Lancez le programme sous le débogueur avec le bouton Lancer ou à l’aide du raccourci clavier F5 :

![Lancer l’application en débogage](media/sdk/speechsdk-17-vs-cpp-f5.png)

Une fenêtre de console vous demandant de dire quelque chose (en anglais) s’affiche.
Le résultat de la reconnaissance s’affiche sur l’écran.

![Sortie de la console après une reconnaissance réussie](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>Téléchargement de l’exemple

Pour obtenir la dernière série d’exemples, consultez le [dépôt GitHub d’exemples pour le kit SDK de reconnaissance vocale Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [page d’exemples](samples.md) pour obtenir des exemples supplémentaires.
