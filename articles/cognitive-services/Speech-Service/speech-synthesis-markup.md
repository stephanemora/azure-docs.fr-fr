---
title: Speech Synthesis Markup Language (SSML) - Services de reconnaissance vocale
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
ms.openlocfilehash: 57fc7e699d88dbe777750e3acdb7f96794b66fc0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837146"
---
# <a name="speech-synthesis-markup-language-ssml"></a>SSML (Speech Synthesis Markup Language)

Le langage de balisage de synthèse vocale (SSML) est un langage de balisage basé sur XML qui fournit un moyen de contrôler la prononciation et la *prosodie* de la synthèse vocale. La prosodie fait référence au rythme et la tonalité de la voix. C’est en quelque sorte sa musique. Vous pouvez indiquer des mots en phonétique, fournir des indications permettant d’interpréter les nombres, insérer des pauses, contrôler la hauteur de la voix, le volume, le débit, etc. Pour plus d’informations, consultez [Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

Pour obtenir la liste complète des langues, paramètres régionaux et voix (neuronales et standard) pris en charge, consultez [prise en charge linguistique](language-support.md#text-to-speech).

Les sections suivantes fournissent des exemples pour les tâches de synthèse vocale courantes.

## <a name="add-a-break"></a>Ajouter une pause
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Modifier le débit

En parlant de taux applicable à voix standard au niveau de la phrase ou word. Tandis que le débit vocal applicable uniquement aux neurones voix au niveau de la phrase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Prononcer
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Modifier le volume

Modifications de volume peuvent être appliquées à voix standard au niveau de la phrase ou word. Tandis que le volume modifications peuvent uniquement être appliquées à une voix NEURONALE au niveau de la phrase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Modifier la tonalité

Modifications de tonalité peuvent être appliquées à voix standard au niveau de la phrase ou word. Tandis que la tonalité modifications peuvent uniquement être appliquées à une voix NEURONALE au niveau de la phrase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Modifier le contour intonatif

> [!IMPORTANT]
> Modifications de contour de tonalité ne sont pas prises en charge avec voix neuronal.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Utiliser plusieurs voix
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Étapes suivantes

* [Prise en charge linguistique : voix, paramètres régionaux, langues](language-support.md)
