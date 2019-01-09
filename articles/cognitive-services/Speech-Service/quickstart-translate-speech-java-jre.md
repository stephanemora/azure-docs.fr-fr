---
title: 'Démarrage rapide : Traduction vocale, Java (Windows, Linux) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer une application Java simple pour capturer les paroles de l’utilisateur, les traduire dans une autre langue et générer le texte à la ligne de commande. Ce guide est conçu pour les utilisateurs de Windows et Linux.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: e0c5139203b360be1eed2292d3ca07c9948b2ca5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729532"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Démarrage rapide : Traduire une entrée vocale à l’aide du kit de développement logiciel (SDK) Speech pour Java

Dans ce guide de démarrage rapide, vous allez créer une application Java simple qui capture les paroles de l’utilisateur à partir du microphone de votre ordinateur, les traduit, puis transcrit le texte traduit à la ligne de commande en temps réel. Cette application est conçue pour une exécution sous Windows 64 bits ou Ubuntu Linux 64 bits 16.04/18.04. Elle est générée avec le package Maven du SDK Speech et l’IDE Eclipse Java (v4.64).

Pour obtenir la liste complète des langues disponibles pour la traduction vocale, consultez [Prise en charge linguistique](language-support.md).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Système d’exploitation : Windows 64 bits ou Ubuntu Linux 64 bits 16.04/18.04
* [IDE Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

Si vous exécutez Ubuntu 16.04/18.04, vérifiez que ces dépendances sont installées avant de démarrer Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
```

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Créer et configurer un projet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)** > **New (Nouvelle)** > **Classe (Classe)**.

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez **speechsdk.quickstart** dans le champ **Package**, et **Main** dans le champ **Name** (Nom).

   ![Capture d’écran de la fenêtre de nouvelle classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Remplacez tout le code `Main.java` par l’extrait de code suivant :

   ```java
   package speechsdk.quickstart;

   import java.io.IOException;
   import java.util.Map;
   import java.util.Scanner;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translationWithMicrophoneAsync() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and service region (e.g., "westus").
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

           // Sets source and target language(s).
           String fromLanguage = "en-US";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage("de");

           // Sets voice name of synthesis output.
           String GermanVoice = "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)";
           config.setVoiceName(GermanVoice);

           // Creates a translation recognizer using microphone as audio input.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           {
               // Subscribes to events.
               recognizer.recognizing.addEventListener((s, e) -> {
                   System.out.println("RECOGNIZING in '" + fromLanguage + "': Text=" + e.getResult().getText());

                   Map<String, String> map = e.getResult().getTranslations();
                   for(String element : map.keySet()) {
                       System.out.println("    TRANSLATING into '" + element + "': " + map.get(element));
                   }
               });

               recognizer.recognized.addEventListener((s, e) -> {
                   if (e.getResult().getReason() == ResultReason.TranslatedSpeech) {
                       System.out.println("RECOGNIZED in '" + fromLanguage + "': Text=" + e.getResult().getText());

                       Map<String, String> map = e.getResult().getTranslations();
                       for(String element : map.keySet()) {
                           System.out.println("    TRANSLATED into '" + element + "': " + map.get(element));
                       }
                   }
                   if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
                       System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
                       System.out.println("    Speech not translated.");
                   }
                   else if (e.getResult().getReason() == ResultReason.NoMatch) {
                       System.out.println("NOMATCH: Speech could not be recognized.");
                   }
               });

               recognizer.synthesizing.addEventListener((s, e) -> {
                   System.out.println("Synthesis result received. Size of audio data: " + e.getResult().getAudio().length);
               });

               recognizer.canceled.addEventListener((s, e) -> {
                   System.out.println("CANCELED: Reason=" + e.getReason());

                   if (e.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
               });

               recognizer.sessionStarted.addEventListener((s, e) -> {
                   System.out.println("\nSession started event.");
               });

               recognizer.sessionStopped.addEventListener((s, e) -> {
                   System.out.println("\nSession stopped event.");
               });

               // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
               System.out.println("Say something...");
               recognizer.startContinuousRecognitionAsync().get();

               System.out.println("Press any key to stop");
               new Scanner(System.in).nextLine();

               recognizer.stopContinuousRecognitionAsync().get();
           }
       }

       public static void main(String[] args) {
           try {
               translationWithMicrophoneAsync();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)** > **Debug (Déboguer)**.

La saisie vocale provenant de votre microphone sera retranscrit en allemand et enregistrée dans la fenêtre console. Appuyez sur « Entrée » pour arrêter la capture vocale.

![Capture d’écran de la sortie de la console après une reconnaissance réussie](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio et générer un texte traduit en parole de synthèse, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Démarrage rapide : reconnaître une entrée vocale, Java (Windows, Linux)](quickstart-java-jre.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
