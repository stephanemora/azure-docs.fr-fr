---
title: Interroger le point de terminaison du conteneur de synthèse vocale
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 4571acfc46d9b336be23eaa3287dce5696c707b2
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930212"
---
Le conteneur fournit des [API de point de terminaison basées sur REST](../rest-text-to-speech.md). Il existe de nombreux [exemples de projets de code source](https://github.com/Azure-Samples/Cognitive-Speech-TTS) pour les variantes de plateforme, d’infrastructure et de langage disponibles.

Avec les conteneurs de synthèse vocale standard et neuronale, vous devez vous appuyer sur les paramètres régionaux et la voix de la balise d’image que vous avez téléchargée. Par exemple, si vous avez téléchargé la balise `latest`, les paramètres régionaux par défaut sont `en-US` et la voix `AriaNeural`. L’argument `{VOICE_NAME}` est alors [`en-US-AriaNeural`](../language-support.md#neural-voices). Consultez l’exemple SSML ci-dessous :

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```
