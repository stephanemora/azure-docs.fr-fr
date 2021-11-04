---
title: Traitement audio avec le Kit de développement logiciel (SDK) Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités, capacités et restrictions du traitement audio à l’aide du Kit de développement logiciel (SDK) Speech.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: bbf774ac1f0b91848df48366436144d36b193033
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097129"
---
# <a name="audio-processing-with-speech-sdk"></a>Traitement audio avec le Kit de développement logiciel (SDK) Speech

Le Kit de développement logiciel (SDK) Speech intègre la pile audio Microsoft (MAS), ce qui permet à toute application ou tout produit d’utiliser ses fonctionnalités de traitement audio sur une entrée audio. La [Configuration minimale requise pour utiliser la pile audio Microsoft](audio-processing-overview.md#minimum-requirements-to-use-microsoft-audio-stack) s’applique.

Les fonctionnalités clés mises à disposition via les API du Kit de développement logiciel (SDK) Speech sont les suivantes :
* **Entrée de microphone en temps réel et entrée de fichier** : le traitement de la pile audio Microsoft peut être appliqué à des entrées de microphone en temps réel, à des flux de diffusion et à entrées basées sur des fichiers. 
* **Sélection des améliorations** : pour permettre le contrôle total de votre scénario, le Kit de développement logiciel (SDK) vous permet de désactiver des améliorations individuelles telles que la suppression de la réverbération, la suppression du bruit, le contrôle de gain automatique et l’annulation de l’écho acoustique. Par exemple, si votre scénario n’inclut pas de rendu d’audio en sortie qui doit être supprimé de l’audio en entrée, vous avez la possibilité de désactiver l’annulation de l’écho acoustique.
* **Géométries de microphones personnalisées** : le Kit de développement logiciel (SDK) vous permet de fournir vos propres informations de géométrie de microphones personnalisées, en plus de la prise en charge de géométries prédéfinies telles que des réseaux linéaires de deux ou quatre micros, et circulaires de sept micros (pour plus d’informations sur les géométries prédéfinies prises en charge, voir [Recommandations sur le réseau de microphones](speech-sdk-microphone.md#microphone-geometry)).
* **Angles de formation de faisceaux** : des angles de formation de faisceaux spécifiques peuvent être fournis pour optimiser les entrées audio provenant d’un emplacement prédéterminé par rapport aux microphones.

Le traitement s’effectue entièrement localement, là où le Kit de développement logiciel (SDK) Speech est utilisé. Aucune donnée audio n’est diffusée en continu vers les services cloud de Microsoft pour traitement par la pile audio Microsoft. La seule exception est le service de transcription de conversation où les données audio brutes sont envoyées aux services cloud de Microsoft pour traitement. 

> [!NOTE]
> Le Kit de développement logiciel (SDK) Speech Devices est désormais déconseillé. Des versions archivées du Kit de développement logiciel (SDK) Speech Devices sont disponibles [ici](speech-devices-sdk.md), avec un exemple de code correspondant disponible sur [GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK). Tous les utilisateurs du Kit de développement logiciel (SDK) Speech Devices sont invités à migrer vers le Kit de développement logiciel (SDK) Speech v1.19 ou version ultérieure.

## <a name="reference-channel-for-echo-cancellation"></a>Canal de référence pour l’annulation de l’écho

La pile audio Microsoft requiert le canal de référence (également appelé canal de bouclage) pour effectuer l’annulation de l’écho. La source du canal de référence varie selon la plateforme :
* **Windows** : le canal de référence est automatiquement collecté par le Kit de développement logiciel (SDK) Speech si l’option `SpeakerReferenceChannel::LastChannel` est fournie lors de la création des `AudioProcessingOptions`.
* **Linux** : l’ALSA (Advanced Linux Sound Architecture) doit être configurée pour fournir le flux audio de référence en tant que dernier canal pour l’appareil d’entrée audio qui sera utilisé. Cela s’ajoute à la fourniture de l’option `SpeakerReferenceChannel::LastChannel` lors de la création des `AudioProcessingOptions`.

## <a name="language-and-platform-support"></a>Prise en charge de langues et de plateformes

| Langage   | Plateforme(s)    | Documents de référence |
|------------|----------------|----------------|
| C++        | Windows, Linux | [Documentation C++](/cpp/cognitive-services/speech/) |
| C#         | Windows, Linux | [Documentation C#](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java       | Windows, Linux | [Documentation Java](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Exemple de code

### <a name="using-microsoft-audio-stack-with-all-default-options"></a>Utilisation de la pile audio Microsoft avec toutes les options par défaut

Cet exemple montre comment utiliser la MAS avec toutes les options d’amélioration par défaut de l’entrée du microphone par défaut de l’appareil.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-a-preset-microphone-geometry"></a>Utilisation de la pile audio Microsoft avec une géométrie de microphones prédéfinie

Cet exemple montre comment utiliser la MAS avec une géométrie de microphones prédéfinie sur un appareil d’entrée audio spécifié. Dans cet exemple :
* **Options d’amélioration** : les améliorations par défaut seront appliquées au flux audio d’entrée.
* **Géométrie prédéfinie** : la géométrie prédéfinie représente un réseau linéaire de deux micros.
* **Appareil d’entrée audio** : l’ID d’appareil d’entrée audio est `hw:0,1`. Pour plus d’informations sur la sélection d’un appareil d’entrée audio, consultez [Guide pratique : Sélectionner un appareil d’entrée audio avec le Kit de développement logiciel (SDK) Speech](how-to-select-audio-input-devices.md).

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
var audioInput = AudioConfig.FromMicrophoneInput("hw:0,1", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry::Linear2);
auto audioInput = AudioConfig::FromMicrophoneInput("hw:0,1", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
AudioConfig audioInput = AudioConfig.fromMicrophoneInput("hw:0,1", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-custom-microphone-geometry"></a>Utilisation de la pile audio Microsoft avec une géométrie de microphones personnalisée

Cet exemple montre comment utiliser la MAS avec une géométrie de microphones personnalisée sur un appareil d’entrée audio spécifié. Dans cet exemple :
* **Options d’amélioration** : les améliorations par défaut seront appliquées au flux audio d’entrée.
* **Géométrie personnalisée** : une géométrie de microphones personnalisée pour un réseau de sept micros est fournie en spécifiant les coordonnées des microphones. Les unités pour les coordonnées sont les millimètres.
* **Entrée audio** : l’entrée audio provient d’un fichier dans lequel l’audio doit être capturé à partir d’un appareil d’entrée audio correspondant à la géométrie personnalisée spécifiée. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7]
{
    new MicrophoneCoordinates(0, 0, 0),
    new MicrophoneCoordinates(40, 0, 0),
    new MicrophoneCoordinates(20, -35, 0),
    new MicrophoneCoordinates(-20, -35, 0),
    new MicrophoneCoordinates(-40, 0, 0),
    new MicrophoneCoordinates(-20, 35, 0),
    new MicrophoneCoordinates(20, 35, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var audioInput = AudioConfig.FromWavFileInput("katiesteve.wav", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Planar,
    { { 0, 0, 0 }, { 40, 0, 0 }, { 20, -35, 0 }, { -20, -35, 0 }, { -40, 0, 0 }, { -20, 35, 0 }, { 20, 35, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto audioInput = AudioConfig::FromWavFileInput("katiesteve.wav", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7];
microphoneCoordinates[0] = new MicrophoneCoordinates(0, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(40, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, -35, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(-20, -35, 0);
microphoneCoordinates[4] = new MicrophoneCoordinates(-40, 0, 0);
microphoneCoordinates[5] = new MicrophoneCoordinates(-20, 35, 0);
microphoneCoordinates[6] = new MicrophoneCoordinates(20, 35, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
AudioConfig audioInput = AudioConfig.fromWavFileInput("katiesteve.wav", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-select-enhancements"></a>Utilisation de la pile audio Microsoft avec des améliorations sélectionnées

Cet exemple montre comment utiliser la MAS avec un ensemble personnalisé d’améliorations de l’audio en entrée. Par défaut, toutes les améliorations sont activées, mais il existe des options pour désactiver la suppression de la réverbération, la suppression du bruit, le contrôle du gain automatique et l’annulation de l’écho individuellement à l’aide des `AudioProcessingOptions`.

Dans cet exemple :
* **Options d’amélioration** : l’annulation de l’écho et la suppression du bruit sont désactivées, tandis que toutes les autres améliorations sont activées.
* **Appareil d’entrée audio** : l’appareil d’entrée audio est le micro par défaut de l’appareil.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-to-specify-beamforming-angles"></a>Utilisation de la pile audio Microsoft pour spécifier des angles de formation de faisceaux

Cet exemple montre comment utiliser la MAS avec une géométrie de microphones personnalisée et des angles de formation de faisceaux sur un appareil d’entrée audio spécifié. Dans cet exemple :
* **Options d’amélioration** : les améliorations par défaut seront appliquées au flux audio d’entrée.
* **Géométrie personnalisée** : une géométrie de microphones personnalisée pour un réseau de quatre micros est fournie en spécifiant les coordonnées des microphones. Les unités pour les coordonnées sont les millimètres.
* **Angles de formation de faisceaux** : des angles de formation de faisceaux sont spécifiés pour optimiser l’audio provenant de cette plage. Les unités pour les angles sont les degrés. Dans l’exemple de code ci-dessous, l’angle de début est défini sur 70 degrés et l’angle de fin sur 110 degrés.
* **Entrée audio** : l’entrée audio provient d’un flux d’envoi (push) dans lequel le flux doit être capturé à partir d’un appareil d’entrée audio correspondant à la géométrie personnalisée spécifiée. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4]
{
    new MicrophoneCoordinates(-60, 0, 0),
    new MicrophoneCoordinates(-20, 0, 0),
    new MicrophoneCoordinates(20, 0, 0),
    new MicrophoneCoordinates(60, 0, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Linear, 70, 110, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var pushStream = AudioInputStream.CreatePushStream();
var audioInput = AudioConfig.FromStreamInput(pushStream, audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Linear,
    70,
    110,
    { { -60, 0, 0 }, { -20, 0, 0 }, { 20, 0, 0 }, { 60, 0, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto pushStream = AudioInputStream::CreatePushStream();
auto audioInput = AudioConfig::FromStreamInput(pushStream, audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4];
microphoneCoordinates[0] = new MicrophoneCoordinates(-60, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(-20, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, 0, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(60, 0, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, 70, 110, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
PushAudioInputStream pushStream = AudioInputStream.createPushStream();
AudioConfig audioInput = AudioConfig.fromStreamInput(pushStream, audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---
