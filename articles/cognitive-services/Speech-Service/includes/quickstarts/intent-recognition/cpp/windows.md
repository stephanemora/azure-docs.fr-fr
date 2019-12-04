---
title: 'Démarrage rapide : Reconnaître la parole, les intentions et les entités, C++ – Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 277548c859370a700973d9f60556fe38e94548b0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280409"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
>
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Créer une application LUIS et obtenir une clé de point de terminaison](../../../../quickstarts/create-luis.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="open-your-project-in-visual-studio"></a>Ouvrez votre projet dans Visual Studio.

La première étape consiste à vérifier que votre projet est ouvert dans Visual Studio.

1. Lancez Visual Studio 2019.
2. Chargez votre projet et ouvrez `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Commencer par du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet. Notez que vous avez créé une méthode asynchrone appelée `recognizeIntent()`.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Avant de pouvoir initialiser un objet `IntentRecognizer`, vous devez créer une configuration qui utilise votre clé et votre région de point de terminaison LUIS. Insérez ce code dans la méthode `recognizeIntent()`.

Cet exemple utilise la méthode `FromSubscription()` pour générer la `SpeechConfig`. Pour obtenir la liste complète des méthodes disponibles, consultez la rubrique [Classe SpeechConfig](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

> [!NOTE]
> Il est important d’utiliser la clé de point de terminaison LUIS, et non les clés de démarrage ou de création, car seule la clé de point de terminaison est valide pour la reconnaissance de conversion de sortie orale en intention. Consultez [Créer une application LUIS et obtenir une clé de point de terminaison](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) pour obtenir des instructions sur la façon d’obtenir la clé appropriée.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

## <a name="initialize-an-intentrecognizer"></a>Initialiser IntentRecognizer

Créons maintenant un `IntentRecognizer`. Insérez ce code dans la méthode `recognizeIntent()`, juste en dessous de votre configuration Speech.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Ajouter un LanguageUnderstandingModel et des intentions

Vous devez maintenant associer un `LanguageUnderstandingModel` au module de reconnaissance de l’intention et ajouter les intentions que vous souhaitez reconnaître.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>Reconnaître une intention

À partir de l’objet `IntentRecognizer`, vous devez appeler la méthode `RecognizeOnceAsync()`. Cette méthode permet au service Speech de savoir que vous envoyez une seule expression pour reconnaissance, et d’arrêter la reconnaissance une fois que l’expression a été identifiée.
Pour faire simple, nous attendons la fin des prochains retours.

À l’intérieur de l’instruction using, ajoutez le code suivant : [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>Afficher les résultats de la reconnaissance (ou les erreurs)

Lorsque le résultat de la reconnaissance est retourné par le service de reconnaissance vocale, vous pouvez effectuer une opération avec celui-ci. Nous allons faire simple et imprimer le résultat dans la console.

À l’intérieur de l’instruction using, sous `RecognizeOnceAsync()`, ajoutez ce code : [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>Vérifier votre code

À ce stade, votre code doit ressembler à ceci :  
(Nous avons ajouté des commentaires à cette version) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>Générer et exécuter votre application

Vous êtes maintenant prêt à créer votre application et à tester la reconnaissance vocale à l’aide du service de reconnaissance vocale.

1. **Compiler le code** : à partir de la barre de menus de Visual Studio, choisissez **Générer** > **Générer la solution**.
2. **Démarrer votre application** : dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.
3. **Démarrer la reconnaissance** : vous êtes invité à prononcer une phrase. Celle-ci est envoyée au service de reconnaissance vocale, transcrite sous forme de texte, puis affichée sur la console.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]