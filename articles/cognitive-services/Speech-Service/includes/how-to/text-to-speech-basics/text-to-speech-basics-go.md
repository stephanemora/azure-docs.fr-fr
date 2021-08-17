---
author: yulin-li
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: yulili
ms.openlocfilehash: 54647867ab0a0d2c1333f80121aeab49a2efc959
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113279860"
---
Dans ce guide de démarrage rapide, vous découvrez les modèles de conception courants qui permettent d’utiliser la synthèse vocale au moyen du kit SDK Speech.

## <a name="skip-to-samples-on-github"></a>Passer aux exemples sur GitHub

Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide Go](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/synthesizer) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech


Avant de pouvoir faire quoi que ce soit, vous devez installer le [kit SDK Speech pour Go](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser).

## <a name="text-to-speech-to-speaker"></a>Conversion de texte par synthèse vocale vers un haut-parleur

Utilisez l’exemple de code suivant pour exécuter la synthèse vocale sur votre appareil de sortie audio par défaut.
Remplacez les variables `subscription` et `region` par votre clé de voix et votre emplacement/région.
L’exécution du script dicte votre texte d’entrée sur le haut-parleur par défaut.

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/common"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func synthesizeStartedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Synthesis started.")
}

func synthesizingHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesizing, audio chunk size %d.\n", len(event.Result.AudioData))
}

func synthesizedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesized, audio length %d.\n", len(event.Result.AudioData))
}

func cancelledHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation.")
}

func main() {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    audioConfig, err := audio.NewAudioConfigFromDefaultSpeakerOutput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        task := speechSynthesizer.SpeakTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        if outcome.Result.Reason == common.SynthesizingAudioCompleted {
            fmt.Printf("Speech synthesized to speaker for text [%s].\n", text)
        } else {
            cancellation, _ := speech.NewCancellationDetailsFromSpeechSynthesisResult(outcome.Result)
            fmt.Printf("CANCELED: Reason=%d.\n", cancellation.Reason)

            if cancellation.Reason == common.Error {
                fmt.Printf("CANCELED: ErrorCode=%d\nCANCELED: ErrorDetails=[%s]\nCANCELED: Did you update the subscription info?\n",
                    cancellation.ErrorCode,
                    cancellation.ErrorDetails)
            }
        }
    }
}
```

Exécutez les commandes suivantes pour créer un fichier `go.mod` lié aux composants hébergés sur GitHub.

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Maintenant, générez et exécutez le code.

```shell
go build
go run quickstart
```

Consultez la documentation de référence pour obtenir des informations détaillées sur les classes [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) et [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer).

## <a name="text-to-speech-to-in-memory-stream"></a>Conversion de texte par synthèse vocale en flux de mémoire

Dans de nombreux scénarios de développement d’application vocale, vous aurez probablement besoin des données audio obtenues sous forme de flux en mémoire plutôt que directement écrites dans un fichier.
Cela vous permettra de générer un comportement personnalisé, notamment :

* Résumer le tableau d’octets obtenu sous forme de flux pouvant faire l’objet de recherches pour des services en aval personnalisés.
* Intégrer le résultat à d’autres API ou services.
* Modifier les données audio, écrire des en-têtes `.wav` personnalisés, etc.

Il est facile d’apporter cette modification à partir de l’exemple précédent. Tout d’abord, supprimez `AudioConfig`, car vous allez gérer le comportement de sortie manuellement à partir de ce point pour un contrôle accru. Transmettez ensuite `nil` pour `AudioConfig` dans le constructeur `SpeechSynthesizer`.

> [!NOTE]
> Le fait de transmettre `nil` pour `AudioConfig`, au lieu de l’omettre comme dans l’exemple de sortie de haut-parleur précédent, n’aura pas pour effet de lire par défaut l’audio sur l’appareil de sortie actuellement actif.

Cette fois, vous allez enregistrer le résultat dans une variable [`SpeechSynthesisResult`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesisResult).
La propriété `AudioData` retourne un élément `[]byte` des données de sortie. Vous pouvez utiliser ce `[]byte` manuellement ou utiliser la classe [`AudioDataStream`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#AudioDataStream) pour gérer le flux en mémoire.
Dans cet exemple, vous allez utiliser la fonction statique `NewAudioDataStreamFromSpeechSynthesisResult()` pour obtenir un flux à partir du résultat.

Remplacez les variables `subscription` et `region` par votre clé de voix et votre emplacement/région.

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main(subscription string, region string) {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, nil)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        // StartSpeakingTextAsync sends the result to channel when the synthesis starts.
        task := speechSynthesizer.StartSpeakingTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        // in most case we want to streaming receive the audio to lower the latency,
        // we can use AudioDataStream to do so.
        stream, err := speech.NewAudioDataStreamFromSpeechSynthesisResult(outcome.Result)
        defer stream.Close()
        if err != nil {
            fmt.Println("Got an error: ", err)
            return
        }

        var all_audio []byte
        audio_chunk := make([]byte, 2048)
        for {
            n, err := stream.Read(audio_chunk)

            if err == io.EOF {
                break
            }

            all_audio = append(all_audio, audio_chunk[:n]...)
        }

        fmt.Printf("Read [%d] bytes from audio data stream.\n", len(all_audio))
    }
}
```

Exécutez les commandes suivantes pour créer un fichier `go.mod` lié aux composants hébergés sur GitHub.

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Maintenant, générez et exécutez le code.

```shell
go build
go run quickstart
```

Consultez la documentation de référence pour obtenir des informations détaillées sur les classes [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) et [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer).

## <a name="use-ssml-to-customize-speech-characteristics"></a>Utiliser SSML pour personnaliser les caractéristiques vocales

Le langage SSML (Speech Synthesis Markup Language) vous permet d’ajuster la tonalité, la prononciation, la vitesse d’élocution, le volume et d’autres éléments de sortie de la synthèse vocale en soumettant vos demandes à partir d’un schéma XML. Cette section présente un exemple de modification de la voix, mais pour obtenir pour des indications plus détaillées, consultez l’[article de guide pratique sur SSML](../../../speech-synthesis-markup.md).

Pour commencer à utiliser SSML pour la personnalisation, vous allez apporter une modification simple qui change la voix.
Tout d’abord, créez un fichier XML pour la configuration SSML dans le répertoire racine de votre projet, à savoir `ssml.xml`. L’élément racine est toujours `<speak>`, et le fait d’intégrer le texte dans un élément `<voice>` vous permet de changer la voix à l’aide du paramètre `name`. Consultez la [liste complète](../../../language-support.md#neural-voices) des voix **neuronales** prises en charge.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Ensuite, vous devez changer la demande de synthèse vocale de sorte qu’elle fasse référence à votre fichier XML.
La demande est presque totalement identique, sauf qu’au lieu d’utiliser la fonction `SpeakTextAsync()`, vous allez utiliser `SpeakSsmlAsync()`. Sachant que cette fonction attend une chaîne XML, vous commencez par charger votre configuration SSML sous forme de chaîne. À partir de là, l’objet obtenu est exactement le même que dans les exemples précédents.

> [!NOTE]
> Pour modifier la voix sans utiliser SSML, vous pouvez définir la propriété sur `SpeechConfig` à l’aide de `speechConfig.SetSpeechSynthesisVoiceName("en-US-AriaNeural")`

## <a name="get-facial-pose-events"></a>Obtenir des événements de pose faciale

La reconnaissance vocale peut être un bon moyen de piloter l’animation des expressions faciales.
Souvent, les [visèmes](../../../how-to-speech-synthesis-viseme.md) sont utilisées pour représenter les principaux éléments de parole observés, tels que la position des lèvres, de la mâchoire et de la langue lors de la production d’un phonème particulier.
Vous pouvez vous abonner à l’événement de visème dans le kit de développement logiciel (SDK) Speech.
Ensuite, vous pouvez appliquer des événements de visème pour animer le visage d’un personnage au fur et à mesure de la lecture audio.
Découvrez [comment obtenir des événements de visème](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
