---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/16/2020
ms.author: pafarley
ms.openlocfilehash: 047269537abefef4599a603b91d2b610bd11caa9
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542514"
---
Le format de streaming audio par défaut est WAV (16 kHz ou 8 kHz, 16 bits et PCM Mono). En plus de WAV/PCM, les formats d’entrées compressées listés ci-dessous sont également pris en charge à l’aide de GStreamer.

- MP3
- OPUS/OGG
- FLAC
- ALAW dans un conteneur wav
- MULAW dans un conteneur wav
- ANY (pour le scénario où le format du média n’est pas connu)
