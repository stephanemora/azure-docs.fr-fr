---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658996"
---
Dans ce guide de démarrage rapide, vous utilisez le [kit SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer une reconnaissance vocale interactive à partir d’entrées de micro, et obtenir la transcription à partir de l’audio capturé. Il est facile d’intégrer cette fonctionnalité à vos applications et à vos appareils pour les tâches de reconnaissance courantes, telles que la transcription des conversations. Elle peut également être utilisée pour des intégrations plus complexes, telles que l’utilisation de Bot Framework avec le SDK Speech pour créer des assistants vocaux.

Après avoir satisfait certaines conditions préalables, la reconnaissance vocale à partir d’un micro se fait en seulement quatre étapes :

> [!div class="checklist"]
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `SpeechRecognizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * À l’aide de l’objet `SpeechRecognizer`, démarrez le processus de reconnaissance pour un seul énoncé.
> * Inspectez le `SpeechRecognitionResult` retourné.
