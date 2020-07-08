---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: f827ea87598a88d399f836f5f412f181a32951ec
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035501"
---
## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech. Suivez les instructions ci-dessous, en fonction de votre plateforme :

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Celle-ci comprend des informations sur votre abonnement, telles que votre clé et la région, le point de terminaison, l’hôte ou le jeton d’autorisation associés.

> [!NOTE]
> Quand vous procédez à une reconnaissance vocale, une synthèse vocale, une traduction ou une reconnaissance intentionnelle, vous devez toujours créer une configuration.

Vous pouvez initialiser une [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) de plusieurs façons :

* Avec un abonnement : transmettez une clé et la région associée.
* Avec un point de terminaison : transmettez un point de terminaison de service Speech. Une clé ou un jeton d’autorisation est facultatif.
* Avec un hôte : transmettez une adresse d’hôte. Une clé ou un jeton d’autorisation est facultatif.
* Avec un jeton d’autorisation : transmettez un jeton d’autorisation et la région associée.

Examinons comment créer une classe [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) à l’aide d’une clé et d’une région. Consultez la page de [prise en charge des régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pour rechercher l’identificateur de votre région.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initialiser un module de reconnaissance

Une fois que vous avez créé une [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable), l’étape suivante consiste à initialiser un [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable). Quand vous initialisez un [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable), vous devez lui transmettre votre `config`. Cela fournit les informations d’identification dont le service Speech a besoin pour valider votre requête.

Si vous effectuez une reconnaissance vocale en utilisant le microphone par défaut de votre appareil, voici à quoi doit ressembler le [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) :

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Si vous souhaitez spécifier le périphérique d’entrée audio, vous devez créer une classe [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) et fournir le paramètre `audioConfig` lors de l’initialisation de votre [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable).

> [!TIP]
> [Découvrez comment obtenir l’ID de votre périphérique d’entrée audio](../../../how-to-select-audio-input-devices.md).

Tout d’abord, ajoutez les instructions `import` suivantes.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Vous pourrez ensuite référencer l’objet `AudioConfig` comme suit :

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Si vous souhaitez fournir un fichier audio au lieu d’utiliser un microphone, vous devez quand même fournir un objet `audioConfig`. Toutefois, quand vous créerez une classe [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable), au lieu d’appeler `fromDefaultMicrophoneInput`, vous appellerez `fromWavFileOutput` et transmettrez le paramètre `filename`.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Reconnaissance vocale

La [classe SpeechRecognizer](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) pour le SDK Speech pour Java expose quelques méthodes que vous pouvez utiliser pour la reconnaissance vocale.

* Reconnaissance unique (async) : effectue la reconnaissance en mode non bloquant (asynchrone). Cela permet de reconnaître un énoncé unique. La fin d’un énoncé unique est déterminée par la détection du silence à la fin, ou après que 15 secondes d’audio ont été traitées.
* Reconnaissance continue (async) : lance de façon asynchrone une opération de reconnaissance continue. Si vous souhaitez fournir un fichier audio au lieu d’utiliser un microphone, vous devez quand même fournir un objet `audioConfig`. Pour arrêter la reconnaissance continue asynchrone, appelez [stopContinuousRecognitionAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync).

> [!NOTE]
> Pour en savoir plus sur la façon de choisir un mode de reconnaissance vocale, consultez [cet article](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Reconnaissance unique

Voici un exemple de reconnaissance asynchrone unique à l’aide de [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable) :

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Vous devrez écrire du code pour gérer le résultat. Cet exemple évalue [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable) :

* Imprime le résultat de la reconnaissance : `ResultReason.RecognizedSpeech`
* S’il n’existe aucune correspondance de reconnaissance, informez l’utilisateur : `ResultReason.NoMatch`
* Si une erreur se produit, imprimez le message d’erreur : `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>Reconnaissance continue

La reconnaissance continue est un peu plus complexe que la reconnaissance unique. Pour obtenir les résultats de la reconnaissance, vous devez vous abonner aux événements `recognizing`, `recognized` et `canceled`. Pour arrêter la reconnaissance, vous devez appeler [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync). Voici un exemple de reconnaissance continue sur un fichier d’entrée audio.

Commençons par définir l’entrée et initialiser un [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) :

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Ensuite, nous allons créer une variable pour gérer l’état de la reconnaissance vocale. Pour commencer, nous allons déclarer un `Semaphore` au niveau de l’étendue de la classe.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Nous allons nous abonner aux événements envoyés à partir du [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable).

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable) : signal pour les événements contenant des résultats de reconnaissance intermédiaires.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable) : signal pour les événements contenant des résultats de reconnaissance finaux (indiquant une tentative de reconnaissance réussie).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable) : signal pour les événements indiquant la fin d’une session de reconnaissance (opération).
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable) : signal pour les événements contenant des résultats de reconnaissance annulés (indiquant une tentative de reconnaissance qui a été annulée suite une requête d’annulation directe ou à un échec de transport ou de protocole).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Tout étant configuré, nous pouvons maintenant appeler [`startContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync).

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Mode de dictée

Quand vous utilisez la reconnaissance continue, vous pouvez activer le traitement de la dictée en utilisant la fonction « enable dictation » correspondante. Dans ce mode, l’instance de configuration vocale interprète les descriptions verbales de structures de phrase comme la ponctuation. Par exemple, l’énoncé « Habitez-vous en ville point d’interrogation » donne le texte « Habitez-vous en ville ? ».

Pour activer le mode dictée, utilisez la méthode [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) sur votre [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Changer la langue source

Une tâche courante pour la reconnaissance vocale consiste à spécifier la langue d’entrée (ou source). Examinons comment choisir le français comme langue d’entrée. Dans votre code, recherchez votre [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable), puis ajoutez cette ligne directement en dessous.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable) est un paramètre qui prend une chaîne comme argument. Vous pouvez fournir n’importe quelle valeur figurant dans la liste des [paramètres régionaux/langues](../../../language-support.md) pris en charge.

## <a name="improve-recognition-accuracy"></a>Améliorer la justesse de la reconnaissance

Il existe plusieurs façons d’améliorer la justesse de la reconnaissance avec le SDK Speech. Jetons un coup d’œil aux listes d’expressions. Les listes d’expressions permettent d’identifier des expressions connues dans les données audio, comme le nom d’une personne ou un lieu spécifique. Il est possible d’ajouter des mots uniques ou des expressions entières à une liste d’expressions. Pendant la reconnaissance, une entrée de liste d’expressions est utilisée si une correspondance exacte pour l’expression entière est incluse dans le contenu audio. Si une correspondance exacte avec l’expression est introuvable, la reconnaissance n’est pas assistée.

> [!IMPORTANT]
> La fonctionnalité Listes d’expressions est disponible uniquement en anglais.

Pour utiliser une liste d’expressions, commencez par créer un objet [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable), puis ajoutez des mots et des expressions spécifiques avec [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_).

Les modifications apportées à [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) prennent effet lors de la reconnaissance suivante ou après une reconnexion au service Speech.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Si vous devez effacer votre liste d’expressions : 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Autres options pour améliorer la justesse de la reconnaissance

Les listes d’expressions ne sont qu’une option parmi d’autres permettant d’améliorer la justesse de la reconnaissance. Vous pouvez également : 

* [Améliorer la justesse avec Custom Speech](../../../how-to-custom-speech.md).
* [Améliorer la justesse avec des modèles de locataire](../../../tutorial-tenant-model.md).
