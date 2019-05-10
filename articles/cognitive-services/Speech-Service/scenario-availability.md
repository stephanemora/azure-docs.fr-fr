---
title: Disponibilité du scénario - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Informations de référence pour les régions du service Speech.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: d844b171ff99dc97e5d1107bcb745f9e8d5b3e9d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519827"
---
# <a name="scenario-availability"></a>Disponibilité du scénario

Le service Speech SDK comprend de nombreux scénarios dans une vaste gamme de langages de programmation et environnements.  Pas tous les scénarios ne sont actuellement disponibles dans tous les langages de programmation ou de tous les environnements encore.  Vous trouverez ci-dessous la disponibilité de chaque scénario.

- **La reconnaissance vocale (SR), liste d’expressions, intention, traduction et On-premises conteneurs**
  - Tous les langages de programmation/environnements où il existe un lien de flèche <img src="media/index/link.jpg" height="15" width="15"></img> dans la table de démarrage rapide [ici](https://aka.ms/csspeech).
- **Synthèse vocale (TTS)**
  - C++/Windows & Linux
  - C#/Windows
  - TTS REST API peut être utilisé dans tous les autres cas.
- **Mise en éveil Word (mot clé cache PERFORE/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows, Linux & Android (appareils de Speech SDK)
  - Fonctionnalités de Word (mot clé cache PERFORE/KWS) de mise en éveil peuvent travailler avec tout type de microphone, officielle KWS prennent en charge, toutefois, est limité aux tableaux microphone figurant actuellement dans le matériel Azure Kinect DK ou les appareils de Speech SDK
- **Assistant virtuel vocal en premier**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux, Mac OS & Android (Kit de développement logiciel de reconnaissance vocale appareils)
- **Transcription de conversation**
  - C++/Windows & Linux
  - C#(Framework et .NET Core) / Windows & UWP & Linux
  - Java/Windows, Linux & Android (appareils de Speech SDK)
- **Transcription de centre d’appels**
  - API REST et peut être utilisé dans n’importe quelle situation
- **Codec compressé l’entrée Audio**
  - C++/Linux
  - C#/Linux
  - Java/Linux et Android
