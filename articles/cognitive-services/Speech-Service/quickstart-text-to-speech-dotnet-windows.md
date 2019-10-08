---
title: 'Démarrage rapide : Reconnaissance vocale, .NET Framework (Windows) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application console de synthèse vocale en utilisant .NET Framework pour Windows et le SDK Speech. Quand vous avez terminé, vous pouvez faire la synthèse vocale à partir d’un texte et entendre ces paroles sur votre haut-parleur en temps réel.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327448"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Démarrage rapide : Synthèse vocale avec le SDK Speech pour .NET Framework (Windows)

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-csharp-dotnet-windows.md) et la [traduction vocale](quickstart-translate-speech-dotnetframework-windows.md).

Utilisez ce guide pour créer une application console de synthèse vocale en utilisant .NET Framework pour Windows et le SDK Speech. Quand vous avez terminé, vous pouvez faire la synthèse vocale à partir d’un texte et entendre ces paroles sur votre haut-parleur en temps réel.

Pour une démonstration rapide (sans générer vous-même le projet Visual Studio, comme décrit dans cet article), consultez les derniers [exemples du SDK Speech de Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) à partir de GitHub.

## <a name="prerequisites"></a>Prérequis

Pour mener à bien ce projet, vous aurez besoin des éléments suivants :

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Clé d’abonnement pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Un haut-parleur (ou un casque) disponible.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez **Program.cs** et remplacez le code généré automatiquement par cet exemple :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Recherchez la chaîne `YourSubscriptionKey` et remplacez-la par votre clé d’abonnement aux services Speech.

1. Recherchez la chaîne `YourServiceRegion`, puis remplacez-la par la [région](regions.md) associée à votre abonnement. Par exemple, si vous utilisez l’abonnement d’essai gratuit, la région est `westus`.

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, choisissez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou sélectionnez **F5**) pour démarrer l’application **helloworld**.

1. Entrez une phrase ou quelques mots en anglais. L’application transmet votre texte aux services Speech, qui envoient une synthèse vocale à l’application pour qu’elle la lise sur votre haut-parleur.

   ![Interface utilisateur de la synthèse vocale](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](record-custom-voice-samples.md)
