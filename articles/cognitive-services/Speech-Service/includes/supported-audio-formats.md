---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/16/2020
ms.author: eur
ms.openlocfilehash: b7a16a63f1ff10987ea4d6c8150c27d7fd8b0afe
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509938"
---
Le format de streaming audio par défaut est WAV (16 kHz ou 8 kHz, 16 bits et PCM Mono). En plus de WAV/PCM, les formats d’entrées compressées listés ci-dessous sont également pris en charge à l’aide de GStreamer.

- MP3
- OPUS/OGG
- FLAC
- ALAW dans un conteneur wav
- MULAW dans un conteneur wav
- ANY (pour le scénario où le format du média n’est pas connu)
