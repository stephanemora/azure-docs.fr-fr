---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: 99b3732c1eaaa208e3fbd172c79b2a7125b6d1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417741"
---
Le format de streaming audio par défaut est WAV (16 kHz ou 8 kHz, 16 bits et PCM Mono). En plus de WAV/PCM, les formats d’entrées compressées listés ci-dessous sont également pris en charge à l’aide de GStreamer.

- MP3
- OPUS/OGG
- FLAC
- ALAW dans un conteneur wav
- MULAW dans un conteneur wav
- ANY (pour le scénario où le format du média n’est pas connu)
