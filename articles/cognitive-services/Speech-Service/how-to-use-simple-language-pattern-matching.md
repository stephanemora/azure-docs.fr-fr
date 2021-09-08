---
title: Comment utiliser les critères spéciaux de langage simple avec le kit de développement logiciel (SDK) Speech C++
titleSuffix: Azure Cognitive Services
description: Dans ce guide, vous allez apprendre à reconnaître les intentions et les entités à partir de modèles simples.
services: cognitive-services
author: chschrae
manager: travisw
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: chschrae
ms.custom: devx-track-cpp
ms.openlocfilehash: abb89fc460915d60402e6b259a8d34c0c5cdf05c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713354"
---
# <a name="how-to-use-simple-language-pattern-matching-with-the-c-speech-sdk"></a>Comment utiliser les critères spéciaux de langage simple avec le kit de développement logiciel (SDK) Speech C++

Cognitive Services [Speech SDK](speech-sdk.md) possède une fonctionnalité intégrée afin d’offrir une **reconnaissance de l’intention** à l’aide des **critères spéciaux de langage simple**. Une intention est quelque chose que l’utilisateur souhaite faire : fermer une fenêtre, marquer une case à cocher, insérer du texte, etc.

Dans ce guide, vous utilisez le SDK Speech pour développer une application console C# qui dégage des intentions à partir des énoncés utilisateur obtenus par le microphone de votre appareil. Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
>
> - Créer un projet Visual Studio faisant référence au package NuGet du kit SDK Speech
> - Créer une configuration de reconnaissance vocale et obtenir un module de reconnaissance de l’intention
> - Ajouter des intentions et des modèles via l’API du kit de développement logiciel (SDK) Speech
> - Utiliser la reconnaissance vocale à partir d’un fichier
> - Utiliser la reconnaissance continue pilotée par événements, asynchrone

## <a name="when-should-you-use-this"></a>Quand l’utiliser ?

Utilisez cet exemple de code dans les cas suivants : 
* Vous êtes uniquement intéressé par la correspondance très stricte avec ce que dit l’utilisateur. Ces modèles correspondent plus radicalement que LUIS.
* Vous n’avez pas accès à une application LUIS, mais vous souhaitez toujours les intentions. Cela peut être utile, car il est incorporé dans le kit de développement logiciel (SDK).
* Vous ne pouvez pas ou ne voulez pas créer une application LUIS, mais vous souhaitez toujours certaines fonctionnalités de commande vocale.

Si vous n’avez pas accès à une application LUIS, mais que vous souhaitez toujours utiliser des intentions, cela peut être utile, car elle est incorporée dans le kit de développement logiciel (SDK).


## <a name="prerequisites"></a>Prérequis

Veillez à disposer des éléments suivants avant de commencer à suivre ce guide :

- Une [ressource de Services cognitifs Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) ou une [ressource Speech unifiée](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (toute édition).

## <a name="speech-and-simple-patterns"></a>Modèles vocaux et simples

Les modèles simples sont une fonctionnalité du kit de développement logiciel (SDK) Speech et nécessitent une ressource de Cognitive Services ou une ressource vocale unifiée.

Un modèle est une expression qui renferme une Entité. Une Entité est définie en encapsulant un mot entre guillemets. Par exemple :

```
    Take me to the {floorName}
```

Cela définit une Entité avec l’ID « floorName », qui respecte la casse.

Tous les autres caractères spéciaux et la ponctuation seront ignorés.

Les intentions seront ajoutées à l’aide d’appels émis vers l’API IntentRecognizer->AddIntent().

## <a name="create-a-speech-project-in-visual-studio"></a>Créer un projet de reconnaissance vocale dans Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

Ouvrez votre projet dans Visual Studio Next, ouvrez votre projet dans Visual Studio.

Lancez Visual Studio 2019.
Chargez votre projet et ouvrez helloworld.cpp.
Commencer avec du code réutilisable. Ajoutons du code qui servira de squelette à notre projet. Notez que vous avez créé une méthode asynchrone appelée recognizeIntent().

## <a name="open-your-project-in-visual-studio"></a>Ouvrez votre projet dans Visual Studio.

Ensuite, ouvrez votre projet dans Visual Studio.

1. Lancez Visual Studio 2019.
2. Chargez votre projet et ouvrez `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Commencer avec du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet

```cpp
    #include <iostream>
    #include <speechapi_cxx.h>

    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Intent;

    int main()
    {
        std::cout << "Hello World!\n";

        auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    }
```

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Avant de pouvoir initialiser un objet `IntentRecognizer`, vous devez créer une configuration qui utilise la clé et la localisation pour la ressource de prédiction de vos Cognitive Services.

* Remplacez `"YOUR_SUBSCRIPTION_KEY"` par la clé de prédiction de vos Cognitive Services.
* Remplacez `"YOUR_SUBSCRIPTION_REGION"` par la région de ressource de vos Cognitive Services.

Cet exemple utilise la méthode `FromSubscription()` pour générer la `SpeechConfig`. Pour obtenir la liste complète des méthodes disponibles, consultez la rubrique [Classe SpeechConfig](/cpp/cognitive-services/speech/speechconfig).

## <a name="initialize-an-intentrecognizer"></a>Initialiser IntentRecognizer

Créez maintenant un `IntentRecognizer`. Insérez ce code juste en dessous de votre configuration Speech.

```cpp
    auto intentRecognizer = IntentRecognizer::FromConfig(config);
```

## <a name="add-some-intents"></a>Ajouter des intentions

Vous devez associer des modèles à `IntentRecognizer` en appelant `AddIntent()`.
Nous allons ajouter 2 intentions avec le même ID pour changer d’étage et une autre intention avec un ID distinct pour ouvrir et fermer les portes.

```cpp
    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");
```

> [!NOTE]
> Il n’existe aucune limite quant au nombre d’entités que vous pouvez déclarer, mais elles seront faiblement mises en correspondance. Si vous ajoutez une expression telle que « {action} porte », elle correspondra à chaque fois qu’il y a du texte avant le mot « porte ». Les intentions sont évaluées en fonction du nombre de leurs entités. Si deux modèles correspondent, celui avec des entités plus définies est retourné.

## <a name="recognize-an-intent"></a>Reconnaître une intention

À partir de l’objet `IntentRecognizer`, vous devez appeler la méthode `RecognizeOnceAsync()`. Cette méthode demande au service Speech de reconnaître la parole en une seule phrase et d’arrêter la reconnaissance vocale une fois que l’expression est identifiée. Pour faire simple, nous attendons la fin des prochains retours.

Insérez ce code sous vos intentions :

```cpp
    std::cout << "Say something ..." << std::endl;
    auto result = intentRecognizer->RecognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>Afficher les résultats de la reconnaissance (ou les erreurs)

Lorsque le résultat de la reconnaissance est retourné par le service Speech, il reste juste à imprimer le résultat.

Insérez ce code en dessous de `auto result = intentRecognizer->RecognizeOnceAsync().get();` :

```cpp
auto entities = result->GetEntities();

switch (result->Reason)
{
case ResultReason::RecognizedSpeech:
case ResultReason::RecognizedIntent:
    std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
    std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
    if (entities.find("floorName") != entities.end())
    {
        std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
    }

    if (entities.find("action") != entities.end())
    {
        std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
    }

    break;
case ResultReason::NoMatch:
{
    auto noMatch = NoMatchDetails::FromResult(result);
    switch (noMatch->Reason)
    {
    case NoMatchReason::NotRecognized:
        std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
        break;
    case NoMatchReason::InitialSilenceTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::InitialBabbleTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::KeywordNotRecognized:
        std::cout << "NOMATCH: Keyword not recognized" << std::endl;
        break;
    }
    break;
}
case ResultReason::Canceled:
{
    auto cancellation = CancellationDetails::FromResult(result);

    if (!cancellation->ErrorDetails.empty())
    {
        std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
        std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
    }
}
default:
    break;
}
```

## <a name="check-your-code"></a>Vérifier votre code

À ce stade, votre code doit ressembler à ceci :

```cpp
#include <iostream>
#include <speechapi_cxx.h>

using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

int main()
{
    auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    auto intentRecognizer = IntentRecognizer::FromConfig(config);

    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");

    std::cout << "Say something ..." << std::endl;

    auto result = intentRecognizer->RecognizeOnceAsync().get();
    auto entities = result->GetEntities();

    switch (result->Reason)
    {
    case ResultReason::RecognizedSpeech:
    case ResultReason::RecognizedIntent:
        std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
        std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
        if (entities.find("floorName") != entities.end())
        {
            std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
        }

        if (entities.find("action") != entities.end())
        {
            std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
        }

        break;
    case ResultReason::NoMatch:
    {
        auto noMatch = NoMatchDetails::FromResult(result);
        switch (noMatch->Reason)
        {
        case NoMatchReason::NotRecognized:
            std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
            break;
        case NoMatchReason::InitialSilenceTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::InitialBabbleTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::KeywordNotRecognized:
            std::cout << "NOMATCH: Keyword not recognized." << std::endl;
            break;
        }
        break;
    }
    case ResultReason::Canceled:
    {
        auto cancellation = CancellationDetails::FromResult(result);

        if (!cancellation->ErrorDetails.empty())
        {
            std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
            std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }
    default:
        break;
    }
}
```
## <a name="build-and-run-your-app"></a>Générer et exécuter votre application

Vous êtes maintenant prêt à créer votre application et à tester la reconnaissance vocale à l’aide du service de reconnaissance vocale.

1. **Compiler le code** : à partir de la barre de menus de Visual Studio, choisissez **Générer** > **Générer la solution**.
2. **Démarrer votre application** : dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur <kbd>F5</kbd>.
3. **Démarrer la reconnaissance** : vous êtes invité à prononcer une phrase. La langue par défaut est l'anglais. Celle-ci est envoyée au service de reconnaissance vocale, transcrite sous forme de texte, puis affichée sur la console.

Par exemple, si vous dites « me faire passer à l’étage 7 », il doit s’agir de la sortie suivante :

```
Say something ...
RECOGNIZED: Text = Take me to floor 7.
  Intent Id = ChangeFloors
  Floor name: = seven
```
