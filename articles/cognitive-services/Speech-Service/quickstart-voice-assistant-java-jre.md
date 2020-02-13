---
title: 'Démarrage rapide : Personnaliser l’assistant vocal pour Java (Windows, Linux) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser le SDK Speech de Cognitive Services dans une application console Java. Vous allez découvrir comment connecter votre application cliente à un bot Bot Framework précédemment créé et configuré pour utiliser le canal Speech Direct Line et activer une expérience de l’assistant vocal.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 45719eebb9cd74b0a5c4278e87b90978dcc3790f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119680"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Démarrage rapide : Créer un assistant vocal avec le SDK Speech Java (préversion)

Les démarrages rapides sont également disponibles pour la [reconnaissance vocale](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre), la [synthèse vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre) et la [traduction vocale](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre).

Dans cet article, vous créez une application console Java en utilisant le [SDK Speech d’Azure Cognitive Services](speech-sdk.md). L’application se connecte à un bot existant configuré pour utiliser le canal Direct Line Speech, envoie une demande vocale et retourne une activité de réponse vocale (si cette option est configurée). L’application est générée avec le package Maven du kit SDK Speech et l’IDE Eclipse Java (v4.8) sur Windows, Ubuntu Linux ou macOS. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

## <a name="prerequisites"></a>Conditions préalables requises

Ce démarrage rapide nécessite :

- Système d’exploitation : Windows (64 bits), Ubuntu Linux 16.04/18.04 (64 bits), ou macOS 10.13 ou ultérieur.
- [IDE Eclipse Java](https://www.eclipse.org/downloads/).
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- Clé d’abonnement Azure pour le service Speech. [Obtenez-en une gratuitement](get-started.md) ou créez-la dans le [portail Azure](https://portal.azure.com).
- Un bot préconfiguré créé avec Bot Framework version 4.2 ou ultérieure. Le bot doit s’abonner au nouveau canal Direct Line Speech pour recevoir des entrées vocales.

  > [!NOTE]
  > Reportez-vous à [la liste des régions prises en charge pour les assistants vocaux](regions.md#voice-assistants) et vérifiez que vos ressources sont déployées dans une de ces régions.

Si vous exécutez Ubuntu 16.04/18.04, vérifiez que ces dépendances sont installées avant de démarrer Eclipse :

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Si vous exécutez Windows (64 bits), vérifiez que vous avez installé Microsoft Visual C++ Redistributable pour votre plateforme :

- [Télécharger Redistributable Visual C++ pour Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Facultatif : Démarrer rapidement

Ce guide de démarrage rapide explique, étape par étape, comment créer une application cliente simple pour vous connecter à votre bot de reconnaissance vocale. Si vous voulez vous y plonger dès maintenant, le code source complet et prêt à être compilé utilisé dans ce guide de démarrage rapide est disponible dans les [exemples du SDK Speech](https://aka.ms/csspeech/samples), sous le dossier `quickstart`.

## <a name="create-and-configure-project"></a>Créer et configurer un projet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

En outre, pour activer la journalisation, mettez à jour le fichier _pom.xml_ en y incluant la dépendance suivante :

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)**  > **New (Nouvelle)**  > **Classe (Classe)** .

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez _speechsdk.quickstart_ dans le champ **Package**, et _Main_ dans le champ **Name** (Nom).

   ![Capture d’écran de la fenêtre de nouvelle classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ouvrez la classe `Main` nouvellement créée et remplacez le contenu du fichier `Main.java` par le code de démarrage suivant :

   ```java
   package speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   import javax.sound.sampled.AudioFormat;
   import javax.sound.sampled.AudioSystem;
   import javax.sound.sampled.DataLine;
   import javax.sound.sampled.SourceDataLine;
   import java.io.InputStream;

   public class Main {
       final Logger log = LoggerFactory.getLogger(Main.class);

       public static void main(String[] args) {
           // New code will go here
       }

       private void playAudioStream(PullAudioOutputStream audio) {
           ActivityAudioStream stream = new ActivityAudioStream(audio);
           final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
           final AudioFormat format = new AudioFormat(
                   AudioFormat.Encoding.PCM_SIGNED,
                   audioFormat.getSamplesPerSecond(),
                   audioFormat.getBitsPerSample(),
                   audioFormat.getChannels(),
                   audioFormat.getFrameSize(),
                   audioFormat.getSamplesPerSecond(),
                   false);
           try {
               int bufferSize = format.getFrameSize();
               final byte[] data = new byte[bufferSize];

               SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
               SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
               line.open(format);

               if (line != null) {
                   line.start();
                   int nBytesRead = 0;
                   while (nBytesRead != -1) {
                       nBytesRead = stream.read(data);
                       if (nBytesRead != -1) {
                           line.write(data, 0, nBytesRead);
                       }
                   }
                   line.drain();
                   line.stop();
                   line.close();
               }
               stream.close();

           } catch (Exception e) {
               e.printStackTrace();
           }
       }

   }
   ```

1. Dans la méthode `main`, vous configurez d’abord votre `DialogServiceConfig` et vous l’utilisez pour créer une instance de `DialogServiceConnector`. Cette instance se connecte au canal Direct Line Speech pour interagir avec votre bot. Une instance `AudioConfig` est également utilisée pour spécifier la source d’entrée audio. Dans cet exemple, le microphone par défaut est utilisé avec `AudioConfig.fromDefaultMicrophoneInput()`.

   - Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement, que vous pouvez obtenir auprès de [ce site web](get-started.md).
   - Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement.

   > [!NOTE]
   > Reportez-vous à [la liste des régions prises en charge pour les assistants vocaux](regions.md#voice-assistants) et vérifiez que vos ressources sont déployées dans une de ces régions.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Le connecteur `DialogServiceConnector` s’appuie sur plusieurs événements pour communiquer ses activités de bot, les résultats de la reconnaissance vocale et d’autres informations. Ajoutez ensuite les écouteurs d’événements suivants.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Connectez `DialogServiceConnector` à Direct Line Speech en appelant la méthode `connectAsync()`. Pour tester votre bot, vous pouvez appeler la méthode `listenOnceAsync` afin d’envoyer l’entrée audio à partir de votre microphone. De plus, vous pouvez utiliser la méthode `sendActivityAsync` pour envoyer une activité personnalisée sous forme de chaîne sérialisée. Ces activités personnalisées peuvent fournir des données supplémentaires que votre bot utilise dans la conversation.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Enregistrez les modifications dans le fichier `Main`.

1. Pour prendre en charge la lecture de la réponse, ajoutez une classe qui transforme l’objet PullAudioOutputStream retourné à partir de l’API getAudio() en un InputStream Java pour en faciliter la gestion. `ActivityAudioStream` est une classe spécialisée qui gère la réponse audio du canal Direct Line Speech. Elle fournit les accesseurs permettant d’extraire les informations du format audio nécessaires pour gérer la lecture. Pour cela, sélectionnez **File (Fichier)**  > **New (Nouveau)**  > **Class (Classe)** .

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez _speechsdk.quickstart_ dans le champ **Package**, et _ActivityAudioStream_ dans le champ **Name** (Nom).

1. Ouvrez la classe `ActivityAudioStream` nouvellement créée et remplacez le contenu par le code suivant :

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer, this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream.
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

   ```

1. Enregistrez les modifications dans le fichier `ActivityAudioStream`.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Sélectionnez F11 ou **Run (Exécuter)**  > **Debug (Déboguer)** .
La console affiche le message « Say something » (Dites quelque chose).
À ce stade, prononcez quelques mots ou une phrase en anglais que votre robot peut comprendre. Vos paroles sont transmises à votre bot via le canal Direct Line Speech, où elles sont reconnues et traitées par votre bot. La réponse est retournée sous la forme d’une activité. Si votre bot retourne des paroles en guise de réponse, les données audio sont lues en utilisant la classe `AudioPlayer`.

![Capture d’écran de la sortie de la console après une reconnaissance réussie](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Créer et déployer un bot de base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Voir aussi

- [À propos des assistants vocaux](voice-assistants.md)
- [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
- [Mots clés personnalisés](speech-devices-sdk-create-kws.md)
- [Connecter Direct Line Speech à votre bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)
