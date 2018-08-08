---
title: Concepts AudioInputStream
description: Vue d’ensemble des fonctionnalités de l’API AudioInputStream.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: b3e12fbc616c8d67b557102c6094467e119a23f1
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281903"
---
# <a name="about-the-audio-input-stream-api"></a>À propos de l’API de flux d’entrée audio

L’API de **flux d’entrée audio** permet de diffuser des flux audio dans les modules de reconnaissance au lieu d’utiliser le microphone ou les API de fichier d’entrée.

## <a name="api-overview"></a>Présentation de l’API

L’API utilise deux composants, le `AudioInputStream` (données audio brutes) et le `AudioInputStreamFormat`.

Le `AudioInputStreamFormat` définit le format des données audio. Il est comparable à la structure `WAVEFORMAT` standard des fichiers wave sur Windows.

  - `FormatTag`

    Le format des fichiers audio. Le kit de développement logiciel (SDK) Speech prend actuellement uniquement en charge `format 1` (PCM - little-endian).

  - `Channels`

    Le nombre de canaux. Le service Speech prend actuellement en charge le matériel audio d’un seul canal (mono).

  - `SamplesPerSec`

    L’échantillonnage. Un enregistrement de microphone classique présente 16 000 échantillons par seconde.

  - `AvgBytesPerSec`

    Octets moyens par seconde, calculés en tant que `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Le nombre moyen d’octets par seconde peut être différent pour les flux audio qui utilisent des débits binaires variables.

  - `BlockAlign`

    La taille d’une seule image, calculée en tant que `Channels * ceil(wBitsPerSample, 8)`. En raison de la marge intérieure, la valeur réelle peut être supérieure à cette valeur.

  - `BitsPerSample`

    Les bits par échantillon. Un flux audio classique utilise 16 bits par échantillon (qualité CD).

La classe de base `AudioInputStream` sera remplacée par votre adaptateur de flux personnalisé. Cet adaptateur doit implémenter les fonctions suivantes :

   - `GetFormat()`

     Cette fonction est appelée pour obtenir le format du flux audio. Elle obtient un pointeur vers la mémoire tampon AudioInputStreamFormat.

   - `Read()`

     Cette fonction est appelée pour obtenir les données du flux audio. Un paramètre est un pointeur vers la mémoire tampon dans laquelle copier les données audio. Le deuxième paramètre est la taille de la mémoire tampon. La fonction renvoie le nombre d’octets copiés dans la mémoire tampon. Une valeur renvoyée de `0` indique la fin du flux.

   - `Close()`

     Cette fonction est appelée pour fermer le flux audio.

## <a name="usage-examples"></a>Exemples d’utilisation

En règle générale, les étapes suivantes sont suivies lors de l’utilisation des flux d’entrée audio :

  - Identifiez le format du flux audio. Le format doit être pris en charge par le kit de développement logiciel (SDK) et le service Speech. La configuration prise en charge actuellement est la suivante :

    Une balise de format audio (PCM), un canal, 16 000 échantillons par seconde, 32 000 octets par seconde, alignement de deux blocs (16 bits, y compris la marge intérieure d’un échantillon), 16 bits par échantillon

  - Assurez-vous que votre code peut fournir les données audio brutes selon les spécifications ci-dessus. Si vos données sources audio ne correspondent pas aux formats pris en charge, le fichier audio doit être converti au format requis.

  - Dérivez votre classe de flux d’entrée audio personnalisé à partir de `AudioInputStream`. Implémentez les opérations `GetFormat()`, `Read()` et `Close()`. La signature de fonction exacte dépend de la langue, mais le code doit ressembler à cet exemple :

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - Utilisez votre flux d’entrée audio :

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - Dans certaines langues, le `contosoStream` doit être supprimé explicitement une fois la reconnaissance terminée. Vous ne pouvez pas libérer le flux audio avant la lecture complète de l’entrée. Dans un scénario utilisant `StopContinuousRecognitionAsync` et `StopContinuousRecognitionAsync`, vous devez posséder un concept illustré dans cet exemple :

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
