---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 70b983d0fc2b13957a3701c778dec074b328a770
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482808"
---
L’une des principales fonctionnalités du service de reconnaissance vocale est la possibilité de reconnaître et de transcrire la voix humaine (souvent appelée « reconnaissance vocale »). Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le SDK de reconnaissance vocale dans vos applications et produits afin d’effectuer une conversion de voix en texte de qualité.

## <a name="skip-to-samples-on-github"></a>Passer aux exemples sur GitHub

Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech. Suivez les instructions ci-dessous, en fonction de votre plateforme :

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Celle-ci comprend des informations sur votre abonnement, telles que votre clé et la région, le point de terminaison, l’hôte ou le jeton d’autorisation associés. Créez une [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) à l’aide de votre clé et de votre région. Consultez la page [Rechercher les clés et la région](../../../overview.md#find-keys-and-region) pour rechercher votre paire clé-région.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Vous pouvez initialiser une [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) de plusieurs autres façons :

* Avec un point de terminaison : transmettez un point de terminaison de service Speech. Une clé ou un jeton d’autorisation est facultatif.
* Avec un hôte : transmettez une adresse d’hôte. Une clé ou un jeton d’autorisation est facultatif.
* Avec un jeton d’autorisation : transmettez un jeton d’autorisation et la région associée.

> [!NOTE]
> Quand vous procédez à une reconnaissance vocale, une synthèse vocale, une traduction ou une reconnaissance intentionnelle, vous devez toujours créer une configuration.

## <a name="recognize-from-microphone"></a>Reconnaître la voix provenant d’un microphone

Pour reconnaître la voix à l’aide du microphone de votre appareil, créez une `AudioConfig` en utilisant `fromDefaultMicrophoneInput()`. Initialisez ensuite un [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable), en transmettant vos `audioConfig` et `config`.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

Si vous souhaitez utiliser un périphérique d’entrée audio *spécifique* , vous devez spécifier son ID dans l’`AudioConfig`. Découvrez [comment obtenir l’ID](../../../how-to-select-audio-input-devices.md) de votre périphérique d’entrée audio.

## <a name="recognize-from-file"></a>Reconnaître la voix à partir d’un fichier

Si vous souhaitez reconnaître la voix à partir d’un fichier audio au lieu d’utiliser un microphone, vous devez quand même créer un objet `AudioConfig`. Toutefois, quand vous créez l’objet [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable), au lieu d’appeler `fromDefaultMicrophoneInput()`, appelez `fromWavFileInput()` et transmettez le chemin du fichier.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>Gestion des erreurs

Les exemples précédents récupèrent simplement le texte reconnu à l’aide de `result.getText()`, mais pour gérer les erreurs et autres réponses, vous devez écrire du code afin de gérer le résultat. L’exemple suivant évalue [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable&preserve-view=true) et :

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

## <a name="continuous-recognition"></a>Reconnaissance continue

Les exemples précédents utilisent la reconnaissance unique, qui reconnaît un énoncé unique. La fin d’un énoncé unique est déterminée par la détection du silence à la fin, ou après que 15 secondes d’audio ont été traitées.

En revanche, la reconnaissance continue est utilisée quand vous souhaitez **contrôler** à quel moment arrêter la reconnaissance. Pour obtenir les résultats de la reconnaissance, vous devez vous abonner aux événements `recognizing`, `recognized` et `canceled`. Pour arrêter la reconnaissance, vous devez appeler [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync). Voici un exemple de reconnaissance continue sur un fichier d’entrée audio.

Commençons par définir l’entrée et initialiser un [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-java-stable) :

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Ensuite, nous allons créer une variable pour gérer l’état de la reconnaissance vocale. Pour commencer, nous allons déclarer un `Semaphore` au niveau de l’étendue de la classe.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Nous allons nous abonner aux événements envoyés à partir du [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-java-stable).

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?preserve-view=true&view=azure-java-stable) : signal pour les événements contenant des résultats de reconnaissance intermédiaires.
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?preserve-view=true&view=azure-java-stable) : signal pour les événements contenant des résultats de reconnaissance finaux (indiquant une tentative de reconnaissance réussie).
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?preserve-view=true&view=azure-java-stable) : signal pour les événements indiquant la fin d’une session de reconnaissance (opération).
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?preserve-view=true&view=azure-java-stable) : signal pour les événements contenant des résultats de reconnaissance annulés (indiquant une tentative de reconnaissance qui a été annulée suite une requête d’annulation directe ou à un échec de transport ou de protocole).

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

Tout étant configuré, nous pouvons maintenant appeler [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync).

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

Pour activer le mode dictée, utilisez la méthode [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?preserve-view=true&view=azure-java-stable) sur votre [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Changer la langue source

Une tâche courante pour la reconnaissance vocale consiste à spécifier la langue d’entrée (ou source). Examinons comment choisir le français comme langue d’entrée. Dans votre code, recherchez votre [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-java-stable), puis ajoutez cette ligne directement en dessous.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?preserve-view=true&view=azure-java-stable) est un paramètre qui prend une chaîne comme argument. Vous pouvez fournir n’importe quelle valeur figurant dans la liste des [paramètres régionaux/langues](../../../language-support.md) pris en charge.

## <a name="improve-recognition-accuracy"></a>Améliorer la justesse de la reconnaissance

Il existe plusieurs façons d’améliorer la justesse de la reconnaissance avec le SDK Speech. Jetons un coup d’œil aux listes d’expressions. Les listes d’expressions permettent d’identifier des expressions connues dans les données audio, comme le nom d’une personne ou un lieu spécifique. Il est possible d’ajouter des mots uniques ou des expressions entières à une liste d’expressions. Pendant la reconnaissance, une entrée de liste d’expressions est utilisée si une correspondance exacte pour l’expression entière est incluse dans le contenu audio. Si une correspondance exacte avec l’expression est introuvable, la reconnaissance n’est pas assistée.

> [!IMPORTANT]
> La fonctionnalité Listes d’expressions est disponible uniquement en anglais.

Pour utiliser une liste d’expressions, commencez par créer un objet [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-java-stable), puis ajoutez des mots et des expressions spécifiques avec [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_).

Les modifications apportées à [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-java-stable) prennent effet lors de la reconnaissance suivante ou après une reconnexion au service Speech.

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