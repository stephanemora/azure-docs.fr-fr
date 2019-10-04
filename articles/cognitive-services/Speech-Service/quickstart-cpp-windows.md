---
title: 'Démarrage rapide : Reconnaissance vocale, C++ (Windows) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir la reconnaissance vocale en C++ sur Windows Desktop à l’aide du SDK Speech
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: a9c43e1d27a396a2c3e9123ce5ce538296c6870c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381884"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Démarrage rapide : Reconnaissance vocale en C++ sur Windows à l’aide du kit SDK de reconnaissance vocale

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](quickstart-text-to-speech-cpp-windows.md) et la [traduction vocale](quickstart-translate-speech-cpp-windows.md).

Si vous le souhaitez, choisissez un autre langage de programmation et un autre environnement :<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console C++ pour Windows. Vous utilisez le [kit SDK Speech](speech-sdk.md) de Cognitive Services pour transcrire la reconnaissance vocale en temps réel à partir du microphone de votre PC. L’application est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2019 (n’importe quelle édition).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, consultez [Essayer le service Speech gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source **helloworld.cpp**.

1. Remplacez tout le code par l’extrait de code suivant :

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application **helloworld**.

1. Prononcez une phrase ou quelques mots en anglais. L’application transmet vos paroles aux services Speech, qui les transcrivent en texte et les renvoient à l’application pour qu’elle les affiche.

   ![Sortie de la console après une reconnaissance réussie](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C++ sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Entraîner un modèle pour Custom Speech](how-to-custom-speech-train-model.md)
