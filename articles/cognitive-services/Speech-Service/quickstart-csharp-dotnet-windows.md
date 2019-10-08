---
title: 'Démarrage rapide : Reconnaissance vocale, .NET Framework (Windows) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application de console de reconnaissance vocale à l’aide de .NET Framework pour Windows et du kit SDK Speech. Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327047"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Démarrage rapide : reconnaissance vocale à l’aide du SDK Speech pour le .NET Framework (Windows)

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](quickstart-text-to-speech-dotnet-windows.md) et la [traduction vocale](quickstart-translate-speech-dotnetframework-windows.md).

Si vous le souhaitez, choisissez un autre langage de programmation et/ou un autre environnement :<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Utilisez ce guide pour créer une application de console de reconnaissance vocale à l’aide de .NET Framework pour Windows et du kit SDK Speech. Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.

Pour une démonstration rapide (sans générer vous-même le projet Visual Studio, comme décrit dans cet article), consultez les derniers [exemples du SDK Speech de Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) à partir de GitHub.

## <a name="prerequisites"></a>Prérequis

Pour mener à bien ce projet, vous aurez besoin des éléments suivants :

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Clé d’abonnement pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Accès au microphone de l’ordinateur.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez **Program.cs** et remplacez le code généré automatiquement par cet exemple :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Recherchez la chaîne `YourSubscriptionKey` et remplacez-la par votre clé d’abonnement aux services Speech.

1. Recherchez la chaîne `YourServiceRegion`, puis remplacez-la par la [région](regions.md) associée à votre abonnement. Par exemple, si vous utilisez l’abonnement d’évaluation gratuite, la région est `westus`.

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Dans la barre de menus, choisissez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou sélectionnez **F5**) pour démarrer l’application **helloworld**.

1. Dites une expression ou une phrase en anglais dans le microphone de votre appareil. L’application transmet vos paroles aux services Speech, qui renvoient le texte transcrit à l’application pour qu’elle les affiche.

   ![Interface utilisateur de la reconnaissance vocale](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Entraîner un modèle pour Custom Speech](how-to-custom-speech-train-model.md)
