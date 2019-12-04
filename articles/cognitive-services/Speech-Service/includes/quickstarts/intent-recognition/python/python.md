---
title: 'Démarrage rapide : Reconnaître la voix, les intentions et les entités, Python - Service de reconnaissance vocale'
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
ms.openlocfilehash: f39ddc8a3460bc026bdac96c18b5bea4d6ecfc0f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280389"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
>
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Créer une application LUIS et obtenir une clé de point de terminaison](../../../../quickstarts/create-luis.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Ouvrir votre projet

Ouvrez Quickstart.py dans votre éditeur Python.

## <a name="start-with-some-boilerplate-code"></a>Commencer par du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Avant de pouvoir initialiser un objet `IntentRecognizer`, vous devez créer une configuration qui utilise votre clé et votre région de point de terminaison LUIS. Ensuite, insérez ce code.

Cet exemple construit l’objet `SpeechConfig` à l’aide de la clé et de la région LUIS. Pour obtenir la liste complète des méthodes disponibles, consultez la rubrique [Classe SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

> [!NOTE]
> Il est important d’utiliser la clé de point de terminaison LUIS, et non les clés de démarrage ou de création, car seule la clé de point de terminaison est valide pour la reconnaissance de conversion de sortie orale en intention. Consultez [Créer une application LUIS et obtenir une clé de point de terminaison](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) pour obtenir des instructions sur la façon d’obtenir la clé appropriée.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>Initialiser IntentRecognizer

Créons maintenant un `IntentRecognizer`. Insérez ce code juste en dessous de votre configuration Speech.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Ajouter un LanguageUnderstandingModel et des intentions

Vous devez maintenant associer un `LanguageUnderstandingModel` au module de reconnaissance de l’intention et ajouter les intentions que vous souhaitez reconnaître.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Reconnaître une intention

À partir de l’objet `IntentRecognizer`, vous devez appeler la méthode `recognize_once()`. Cette méthode permet au service Speech de savoir que vous envoyez une seule expression pour reconnaissance, et d’arrêter la reconnaissance une fois que l’expression a été identifiée.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Afficher les résultats de la reconnaissance (ou les erreurs)

Lorsque le résultat de la reconnaissance est retourné par le service de reconnaissance vocale, vous pouvez effectuer une opération avec celui-ci. Nous allons faire simple et imprimer le résultat dans la console.

À l’intérieur de l’instruction using, sous votre appel à `recognize_once()`, ajoutez ce code : [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Vérifier votre code

À ce stade, votre code doit ressembler à ceci :  
(Nous avons ajouté des commentaires à cette version) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Générer et exécuter votre application

Exécutez l’exemple à partir de la console ou dans votre IDE :

    ````
    python quickstart.py
    ````

Les 15 secondes suivantes de saisie vocale provenant de votre microphone seront reconnues et enregistrées dans la fenêtre console.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
