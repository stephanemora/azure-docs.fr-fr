---
title: Langage de balisage de synthèse vocale (SSML) - Services Speech
titleSuffix: Azure Cognitive Services
description: Utilisation du langage de balisage de synthèse vocale pour contrôler la prononciation et la prosodie dans la synthèse vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a748250a25c483f61489351943e8ef29a5d5edbe
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165068"
---
# <a name="speech-synthesis-markup-language-ssml"></a>SSML (Speech Synthesis Markup Language)

Le langage de balisage de synthèse vocale (SSML) est un langage de balisage basé sur XML qui fournit un moyen de contrôler la prononciation et la *prosodie* de la synthèse vocale. La prosodie fait référence au rythme et la tonalité de la voix. C’est en quelque sorte sa musique. Vous pouvez indiquer des mots en phonétique, fournir des indications permettant d’interpréter les nombres, insérer des pauses, contrôler la hauteur de la voix, le volume, le débit, etc. Pour plus d’informations, consultez [Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). 

Pour obtenir la liste complète des langues, paramètres régionaux et voix (neuronales et standard) pris en charge, consultez [prise en charge linguistique](language-support.md#text-to-speech).

Les sections suivantes fournissent des exemples pour les tâches de synthèse vocale courantes.

## <a name="adjust-speaking-style-for-neural-voices"></a>Ajuster le style d’élocution pour les voix neuronales

Vous pouvez utiliser SSML pour ajuster le style d’élocution lorsque vous utilisez une des voix neuronales.

Par défaut, le service de synthèse vocale synthétise le texte dans un style neutre. Les voix neuronales étendent SSML avec un élément `<mstts:express-as>` qui convertit le texte pour la synthèse vocale dans différents styles d’élocution. Actuellement, les balises de style sont uniquement prises en charge avec ces voix :

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

Les modifications de style d’élocution peuvent être appliquées au niveau de la phrase. Les styles varient selon la voix. Si un type de style n’est pas pris en charge, le service renvoie la synthèse vocale avec le style neutre par défaut.

| Voix | Style | Description | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Exprime une émotion positive et heureuse |
| | type=`empathy` | Exprime une de la compassion et de la compréhension |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Exprime un ton formel, similaire aux journaux télévisés |
| | type=`sentiment` | Transmet un message ou récit touchant |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>Ajouter une pause
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Modifier le débit

La cadence d’élocution peut s’appliquer aux voix standard au niveau de la phrase ou du mot. Cependant, la cadence d’élocution ne s’applique aux voix neuronales qu’au niveau de la phrase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Prononcer
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Modifier le volume

Les modifications de volume peuvent s’appliquer aux voix standard au niveau de la phrase ou du mot. Cependant, les modifications de volume ne s’appliquent aux voix neuronales qu’au niveau de la phrase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Modifier la tonalité

Les modifications de ton peuvent s’appliquer aux voix standard au niveau de la phrase ou du mot. Cependant, les modifications de ton ne s’appliquent aux voix neuronales qu’au niveau de la phrase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Modifier le contour intonatif

> [!IMPORTANT]
> Les modifications de contour de ton ne sont pas prises en charge avec les voix neuronales.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Utiliser plusieurs voix
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Étapes suivantes

* [Prise en charge linguistique : voix, paramètres régionaux, langues](language-support.md)
