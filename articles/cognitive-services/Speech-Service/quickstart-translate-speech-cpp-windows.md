---
title: 'Démarrage rapide : Traduire une entrée vocale, C++ (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer une application C++ pour capturer les paroles de l’utilisateur, les traduire dans une autre langue et générer le texte sur la ligne de commande. Ce guide est conçu pour les utilisateurs de Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382672"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Démarrage rapide : Traduire des paroles en C++ sur Windows avec le SDK Speech

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-cpp-windows.md) et la [synthèse vocale](quickstart-text-to-speech-cpp-windows.md).

Dans ce guide de démarrage rapide, vous allez créer une application C++ qui capture les paroles de l’utilisateur à partir du microphone de votre ordinateur, les traduit, puis transcrit le texte traduit sur la ligne de commande en temps réel. Cette application est générée avec le [package NuGet du SDK Speech](https://aka.ms/csspeech/nuget) et Microsoft Visual Studio 2019 (n’importe quelle édition).

Pour obtenir la liste complète des langues disponibles pour la traduction vocale, consultez [Prise en charge linguistique](language-support.md).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Pour plus d’informations, consultez [Essayer le service Speech gratuitement](get-started.md).

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source **helloworld.cpp**.

1. Remplacez tout le code par l’extrait de code suivant :

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application **helloworld**.

1. Prononcez une phrase ou quelques mots en anglais. L’application transmet vos paroles aux services Speech, qui traduisent et transcrivent les paroles en texte (dans ce cas, vers le français et l’allemand). Les services Speech renvoient ensuite le texte à l’application pour que celle-ci l’affiche.

   ![Sortie de la console après une traduction vocale réussie](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée vocale à partir d’un fichier audio ou convertir du texte traduit en synthèse vocale, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples C++ sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Entraîner un modèle pour Custom Speech](how-to-custom-speech-train-model.md)
