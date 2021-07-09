---
title: Comment obtenir des événements de pose faciale pour la synchronisation labiale
titleSuffix: Azure Cognitive Services
description: Le SDK Speech prend en charge les événements de visème pendant la synthèse vocale, qui représentent les éléments clés dans la prononciation observée, comme la position des lèvres, de la mâchoire et de la langue lors de la production d’un phonème particulier.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 7ef3e07eb1585aaa87986fd682b4db00c53e66f3
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109810675"
---
# <a name="get-facial-pose-events"></a>Obtenir des événements de pose faciale

> [!NOTE]
> Les événements de visème sont disponibles uniquement pour les [voix neuronales](language-support.md#text-to-speech) anglaises (États-Unis) `en-US` pour l’instant.

Un _visème_ est la description visuelle d’un phonème dans le langage parlé.
Il définit la position du visage et de la bouche quand vous prononcez un mot.
Chaque visème représente les caractéristiques principales du visage pour un ensemble spécifique de phonèmes.
Il n’existe pas de correspondance un-à-un entre les visèmes et les phonèmes.
Souvent, plusieurs phonèmes correspondent à un seul visème, car plusieurs phonèmes semblent identiques sur le visage lors de leur production, comme `s` et `z`.
Consultez la [table de mappage entre visèmes et phonèmes](#map-phonemes-to-visemes).

À l’aide de visèmes, vous pouvez créer un assistant de diffusion d’informations plus naturel et intelligent, des personnages de jeu et de dessin animé plus interactifs, ainsi que des vidéos d’apprentissage linguistique plus intuitives. Les sourds et malentendants peuvent également repérer les sons visuellement et « lire sur les lèvres » du contenu qui affiche des visèmes sur un visage animé.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Recevoir des événements de visème avec le kit de développement logiciel (SDK) Speech

Pour créer des événements de visème, le service TTS convertit le texte d’entrée en un ensemble de séquences de phonème et leurs séquences de visème correspondantes.
Ensuite, le point de départ de chaque visème est estimé dans l’audio parlé.
Les événements de visème contiennent une séquence d’ID de visème, chacun avec un décalage dans l’audio où ce visème apparaît.
Ces événements peuvent conduire à des animations de bouche qui simulent une personne prononçant le texte d’entrée.

| Paramètre | Description |
|-----------|-------------|
| ID de visème | Nombre entier qui spécifie un visème. En anglais (États-Unis), nous proposons 22 visèmes différents pour représenter les formes de bouche pour un ensemble spécifique de phonèmes. Consultez la [table de mappage entre ID de visème et phonèmes](#map-phonemes-to-visemes).  |
| Décalage audio | Temps au début de chaque visème, en cycles (100 nanosecondes). |

Pour accéder aux événements de visème, abonnez-vous à l’événement `VisemeReceived` dans le SDK Speech.
Les extraits de code suivants montrent comment s’abonner à l’événement de visème.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Mapper les phonèmes à des visèmes

Les visèmes varient selon la langue. Chaque langue possède un ensemble de visèmes qui correspondent à ses phonèmes spécifiques. Le tableau suivant montre la correspondance entre les phonèmes de l’alphabet phonétique international (IPA) et les ID de visème pour l’anglais (États-Unis).

| IPA | Exemple | ID de visème |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** very | 4 |
|æ  |   **a** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **a** go           |1|
|ɪɹ |   **ear** s          |[6, 13]|
|ɛɹ |   **air** plane      |[4, 13]|
|ʊɹ |   c **ur** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** land   |[11, 13]|
|aʊ(ə)ɹ |   **hour** s     |[9, 13]|
|ɔɹ |   **or** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **ear** th         |[5, 13]|
|ɚ  |   all **er** gy       |[1, 13]|
|w  |   **w** ith, s **ue** de   |7|
|j  |   **y** ard, f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|d  |   **d** ig           |19|
|k  |   **c** ut           |20|
|g  |   **g** o            |20|
|m  |   **m** at, s **m** ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
|s  |   **s** it           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** id, g **l** ad     |14|
|ɹ  |   **r** ed, b **r** ing    |13|


## <a name="next-steps"></a>Étapes suivantes

* [Documentation de référence du SDK Speech](speech-sdk.md)
