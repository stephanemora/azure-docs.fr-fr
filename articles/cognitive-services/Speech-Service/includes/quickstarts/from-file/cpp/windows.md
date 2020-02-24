---
title: 'Démarrage rapide : Reconnaître la voix d’un fichier audio, C++ (Windows) – Service Speech'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: cfe649f56ff57548f6fa711abf67f2925d74a2ea
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446383"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez le fichier source **helloworld.cpp**.

1. Remplacez tout le code par l’extrait de code suivant :
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par l’**identificateur Région** de la [région](https://aka.ms/speech/sdkregion) qui est associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Remplacez la chaîne `whatstheweatherlike.wav` par le nom de votre fichier.

1. Dans la barre de menus, choisissez **Fichier** > **Enregistrer tout**.

> [!NOTE]
> Le SDK Speech reconnaît par défaut l’utilisation de la langue en-US. Consultez [Spécifier la langue source pour la reconnaissance vocale](../../../../how-to-specify-source-language.md) pour plus d’informations sur le choix de la langue source.

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

1. Dans la barre de menus, sélectionnez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application **helloworld**.

1. Votre fichier audio est transmis au service Speech et le premier énoncé du fichier est transcrit en texte, qui apparaît dans la même fenêtre.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]