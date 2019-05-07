---
title: 'Démarrage rapide : Personnaliser l’assistant virtuel « voice-first » (préversion), Java (Windows, Linux) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le kit SDK Speech de Cognitive Services dans une application console Java. Vous allez apprendre à connecter votre application cliente à un bot Bot Framework existant configuré pour utiliser le canal Direct Line Speech et à procurer une expérience de l’assistant virtuel « voice-first ».
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025364"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Démarrage rapide : Créer un assistant virtuel « voice-first » avec le SDK Speech, Java

Dans cet article, vous créez une application console Java au moyen du [kit SDK Speech de Cognitive Services](speech-sdk.md). L’application se connecte à un bot existant configuré pour utiliser le canal Direct Line Speech, envoie une demande vocale et retourne une activité de réponse vocale (si cette option est configurée). L’application est générée avec le package Maven du kit SDK Speech et l’IDE Eclipse Java (v4.8) sur Windows, Ubuntu Linux ou macOS. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Système d’exploitation : Windows (64 bits), Ubuntu Linux 16.04/18.04 (64 bits), ou macOS 10.13 ou ultérieur
* [IDE Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Un bot préconfiguré créé à l’aide de Bot Framework version 4.2 ou ultérieure. Le bot doit s’abonner au nouveau canal « Direct Line Speech » pour recevoir des entrées vocales.

    > [!NOTE]
    > Dans la préversion, le canal Direct Line Speech prend uniquement en charge la région **westus2**.

    > [!NOTE]
    > La version d’évaluation de 30 jours pour le niveau tarifaire standard décrite dans [Essayer les services Speech gratuitement](get-started.md) étant limitée à la région **westus** (et non à **westus2**), elle n’est pas compatible avec Direct Line Speech. Les abonnements **westus2** des niveaux gratuit et standard sont compatibles.

Si vous exécutez Ubuntu 16.04/18.04, vérifiez que ces dépendances sont installées avant de démarrer Eclipse :

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Si vous exécutez Windows (64 bits), vérifiez que vous avez installé Microsoft Visual C++ Redistributable pour votre plateforme :
* [Télécharger Redistributable Visual C++ pour Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Facultatif : Démarrer rapidement

Ce guide de démarrage rapide explique, étape par étape, comment créer une application cliente simple pour vous connecter à votre bot de reconnaissance vocale. Si vous préférez vous y plonger dès maintenant, le code source complet et prêt à compiler utilisé dans ce guide de démarrage rapide est disponible dans les [exemples du SDK Speech](https://aka.ms/csspeech/samples) sous le dossier `quickstart`.

## <a name="create-and-configure-project"></a>Créer et configurer un projet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

En outre, pour activer la journalisation, mettez à jour le fichier **pom.xml** afin d’inclure la dépendance suivante.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)** > **New (Nouvelle)** > **Classe (Classe)**.

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez **speechsdk.quickstart** dans le champ **Package**, et **Main** dans le champ **Name** (Nom).

   ![Capture d’écran de la fenêtre de nouvelle classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ouvrez la classe **Main** créée et remplacez le contenu du fichier `Main.java` par le code initial suivant.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. Dans la méthode **main**, vous allez tout d’abord configurer votre `BotConnectorConfig` et l’utiliser pour créer une instance `SpeechBotConnector`. Ce connecteur se connectera au canal Direct Line Speech pour interagir avec votre bot. Une instance `AudioConfig` est également utilisée pour spécifier la source d’entrée audio. Dans cet exemple, le microphone par défaut est utilisé avec `AudioConfig.fromDefaultMicrophoneInput()`.

    * Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement, que vous pouvez obtenir [ici](get-started.md).
    * Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement.
    * Remplacez la chaîne `YourChannelSecret` par le secret de votre canal Direct Line Speech.

    > [!NOTE]
    > Dans la préversion, le canal Direct Line Speech prend uniquement en charge la région **westus2**.

    > [!NOTE]
    > La version d’évaluation de 30 jours pour le niveau tarifaire standard décrite dans [Essayer les services Speech gratuitement](get-started.md) étant limitée à la région **westus** (et non à **westus2**), elle n’est pas compatible avec Direct Line Speech. Les abonnements **westus2** des niveaux gratuit et standard sont compatibles.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` s’appuie sur plusieurs événements pour communiquer ses activités de bot, les résultats de la reconnaissance vocale et d’autres informations. Ajoutez ensuite les écouteurs d’événements suivants.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Connectez `SpeechBotConnector` à Direct Line Speech en appelant la méthode `connectAsync()`. Pour tester votre bot, vous pouvez appeler la méthode `listenOnceAsync` afin d’envoyer l’entrée audio à partir de votre microphone. De plus, vous pouvez utiliser la méthode `sendActivityAsync` pour envoyer une activité personnalisée sous forme de chaîne sérialisée. Ces activités personnalisées peuvent fournir des données supplémentaires susceptibles d’être utilisées par votre bot dans la conversation.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Enregistrez les modifications dans le fichier `Main`.

1. Pour prendre en charge la lecture de la réponse, vous allez ajouter une classe qui inclut des méthodes utilitaires assurant la prise en charge de la fonctionnalité audio. Pour activer la fonctionnalité audio, ajoutez une nouvelle classe vide à votre projet Java : sélectionnez **File** > **New** > **Class**.

1. Dans la fenêtre **New Java Class**, entrez **speechsdk.quickstart** dans le champ **Package**, et **AudioPlayer** dans le champ **Name**.

   ![Capture d’écran de la fenêtre de nouvelle classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ouvrez la classe **AudioPlayer** créée et remplacez son contenu par le code fourni ci-dessous.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Enregistrez les modifications dans le fichier `AudioPlayer`.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)** > **Debug (Déboguer)**.
La console affiche un message « Say something » (Dites quelque chose) ; à ce stade, vous pouvez prononcer une phrase en anglais ou une phrase compréhensible par votre bot. Votre production orale sera transmis à votre bot via le canal Direct Line Speech où il sera reconnu, puis traité par votre bot, avant que la réponse ne soit retournée en tant qu’activité. Si votre bot retourne une production orale en guise de réponse, les données audio sont lues à l’aide de la classe `AudioPlayer`.

![Capture d’écran de la sortie de la console après une reconnaissance réussie](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Démarrage rapide : traduire une entrée vocale, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
