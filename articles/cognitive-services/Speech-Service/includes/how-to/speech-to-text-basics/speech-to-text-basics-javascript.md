---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: a46efdc1ca1391e5a8002bacca6a86b4d82eae96
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035607"
---
## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK Speech pour JavaScript<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Suivez les instructions ci-dessous, en fonction de votre plateforme :

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navigateur web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

De plus, selon l’environnement cible, utilisez l’un des éléments suivants :

# <a name="import"></a>[import](#tab/import)

```javascript
import {
    AudioConfig,
    CancellationDetails,
    CancellationReason,
    PhraseListGrammar,
    ResultReason,
    SpeechConfig,
    SpeechRecognizer
} from "microsoft-cognitiveservices-speech-sdk";
```

Pour plus d’informations sur `import`, consultez la page consacrée aux directives <a href="https://javascript.info/import-export" target="_blank">export et import <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Pour plus d’informations sur `require`, consultez la <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">présentation de require <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[script](#tab/script)

Téléchargez et extrayez le fichier *microsoft.cognitiveservices.speech.sdk.bundle.js* du <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK Speech pour JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a>, puis placez-le dans un dossier accessible à votre fichier HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Si vous ciblez un navigateur web et que vous utilisez l’étiquette `<script>`, le préfixe `sdk` n’est pas nécessaire. Le préfixe `sdk` est un alias utilisé pour nommer le module `require`.

---

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest). Celle-ci comprend des informations sur votre abonnement, telles que votre clé et la région, le point de terminaison, l’hôte ou le jeton d’autorisation associés.

> [!NOTE]
> Quand vous procédez à une reconnaissance vocale, une synthèse vocale, une traduction ou une reconnaissance intentionnelle, vous devez toujours créer une configuration.

Vous pouvez initialiser une [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) de plusieurs façons :

* Avec un abonnement : transmettez une clé et la région associée.
* Avec un point de terminaison : transmettez un point de terminaison de service Speech. Une clé ou un jeton d’autorisation est facultatif.
* Avec un hôte : transmettez une adresse d’hôte. Une clé ou un jeton d’autorisation est facultatif.
* Avec un jeton d’autorisation : transmettez un jeton d’autorisation et la région associée.

Examinons comment créer une classe [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) à l’aide d’une clé et d’une région. Consultez la page de [prise en charge des régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pour rechercher l’identificateur de votre région.

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initialiser un module de reconnaissance

Une fois que vous avez créé une [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest), l’étape suivante consiste à initialiser un [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest). Quand vous initialisez un [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest), vous devez lui transmettre votre `speechConfig`. Cela fournit les informations d’identification dont le service Speech a besoin pour valider votre requête.

Si vous effectuez une reconnaissance vocale en utilisant le microphone par défaut de votre appareil, voici à quoi doit ressembler le [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) :

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Si vous souhaitez spécifier le périphérique d’entrée audio, vous devez créer une classe [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) et fournir le paramètre `audioConfig` lors de l’initialisation de votre [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest).

> [!TIP]
> [Découvrez comment obtenir l’ID de votre périphérique d’entrée audio](../../../how-to-select-audio-input-devices.md).

Référencez l’objet `AudioConfig` comme suit :

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Si vous souhaitez fournir un fichier audio au lieu d’utiliser un microphone, vous devez quand même fournir un objet `audioConfig`. Toutefois, cette opération ne peut être effectuée que si vous ciblez **Node.js**, et quand vous créez un [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest), au lieu d’appeler `fromDefaultMicrophoneInput`, vous devez appeler `fromWavFileOutput` et passer le paramètre `filename`.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Reconnaissance vocale

La [classe Recognizer](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) pour le SDK Speech pour JavaScript expose quelques méthodes que vous pouvez utiliser pour la reconnaissance vocale.

* Reconnaissance unique (async) : effectue la reconnaissance en mode non bloquant (asynchrone). Cela permet de reconnaître un énoncé unique. La fin d’un énoncé unique est déterminée par la détection du silence à la fin, ou après que 15 secondes d’audio ont été traitées.
* Reconnaissance continue (async) : lance de façon asynchrone une opération de reconnaissance continue. L’utilisateur s’inscrit auprès des événements et gère l’état de l’application. Pour arrêter la reconnaissance continue asynchrone, appelez [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync).

> [!NOTE]
> Pour en savoir plus sur la façon de choisir un mode de reconnaissance vocale, consultez [cet article](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Reconnaissance unique

Voici un exemple de reconnaissance asynchrone unique à l’aide de [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Vous devrez écrire du code pour gérer le résultat. Cet exemple évalue [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest#reason) :

* Imprime le résultat de la reconnaissance : `ResultReason.RecognizedSpeech`
* S’il n’existe aucune correspondance de reconnaissance, informez l’utilisateur : `ResultReason.NoMatch`
* Si une erreur se produit, imprimez le message d’erreur : `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        console.log("    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

### <a name="continuous-recognition"></a>Reconnaissance continue

La reconnaissance continue est un peu plus complexe que la reconnaissance unique. Pour obtenir les résultats de la reconnaissance, vous devez vous abonner aux événements `Recognizing`, `Recognized` et `Canceled`. Pour arrêter la reconnaissance, vous devez appeler [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync). Voici un exemple de reconnaissance continue sur un fichier d’entrée audio.

Commençons par définir l’entrée et initialiser un [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) :

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Nous allons nous abonner aux événements envoyés à partir du [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest).

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizing) : signal pour les événements contenant des résultats de reconnaissance intermédiaires.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognized) : signal pour les événements contenant des résultats de reconnaissance finaux (indiquant une tentative de reconnaissance réussie).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#sessionstopped) : signal pour les événements indiquant la fin d’une session de reconnaissance (opération).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#canceled) : signal pour les événements contenant des résultats de reconnaissance annulés (indiquant une tentative de reconnaissance qui a été annulée suite une requête d’annulation directe ou à un échec de transport ou de protocole).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Tout étant configuré, nous pouvons maintenant appeler [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#startcontinuousrecognitionasync).

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Mode de dictée

Quand vous utilisez la reconnaissance continue, vous pouvez activer le traitement de la dictée en utilisant la fonction « enable dictation » correspondante. Dans ce mode, l’instance de configuration vocale interprète les descriptions verbales de structures de phrase comme la ponctuation. Par exemple, l’énoncé « Habitez-vous en ville point d’interrogation » donne le texte « Habitez-vous en ville ? ».

Pour activer le mode dictée, utilisez la méthode [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--) sur votre [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Changer la langue source

Une tâche courante pour la reconnaissance vocale consiste à spécifier la langue d’entrée (ou source). Examinons comment choisir l’italien comme langue d’entrée. Dans votre code, recherchez votre [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest), puis ajoutez cette ligne directement en dessous.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

La propriété [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#speechrecognitionlanguage) attend une chaîne au format langue-paramètres régionaux. Vous pouvez fournir n’importe quelle valeur figurant dans la colonne **Paramètres régionaux** dans la liste des [paramètres régionaux/langues](../../../language-support.md) pris en charge.

## <a name="improve-recognition-accuracy"></a>Améliorer la justesse de la reconnaissance

Il existe plusieurs façons d’améliorer la justesse de la reconnaissance avec le SDK Speech. Jetons un coup d’œil aux listes d’expressions. Les listes d’expressions permettent d’identifier des expressions connues dans les données audio, comme le nom d’une personne ou un lieu spécifique. Il est possible d’ajouter des mots uniques ou des expressions entières à une liste d’expressions. Pendant la reconnaissance, une entrée de liste d’expressions est utilisée si une correspondance exacte pour l’expression entière est incluse dans le contenu audio. Si une correspondance exacte avec l’expression est introuvable, la reconnaissance n’est pas assistée.

> [!IMPORTANT]
> La fonctionnalité Listes d’expressions est disponible uniquement en anglais.

Pour utiliser une liste d’expressions, commencez par créer un objet [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest), puis ajoutez des mots et des expressions spécifiques avec [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest#addphrase-string-).

Les modifications apportées à [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) prennent effet lors de la reconnaissance suivante ou après une reconnexion au service Speech.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Si vous devez effacer votre liste d’expressions :

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Autres options pour améliorer la justesse de la reconnaissance

Les listes d’expressions ne sont qu’une option parmi d’autres permettant d’améliorer la justesse de la reconnaissance. Vous pouvez également : 

* [Améliorer la justesse avec Custom Speech](../../../how-to-custom-speech.md).
* [Améliorer la justesse avec des modèles de locataire](../../../tutorial-tenant-model.md).
