---
title: 'Démarrage rapide : Synthétiser la voix, C# (Windows) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application console de synthèse vocale en utilisant .NET Framework pour Windows et le SDK Speech. Quand vous avez terminé, vous pouvez faire la synthèse vocale à partir d’un texte et entendre ces paroles sur votre haut-parleur en temps réel.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: b615ba8085650e9aa686fb4a229d9752c4f6e2ce
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925626"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez **Program.cs** et remplacez le code généré automatiquement par cet exemple :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Recherchez la chaîne `YourSubscriptionKey` et remplacez-la par votre clé d’abonnement au service Speech.

1. Recherchez la chaîne `YourServiceRegion`, puis remplacez-la par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement. Par exemple, si vous utilisez l’abonnement d’essai gratuit, la région est `westus`.

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, choisissez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou sélectionnez **F5**) pour démarrer l’application **helloworld**.

1. Entrez une phrase ou quelques mots en anglais. L’application transmet votre texte au service Speech, qui envoie une synthèse vocale à l’application pour qu’elle la lise sur votre haut-parleur.

   ![Interface utilisateur de la synthèse vocale](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
