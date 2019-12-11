---
title: À propos du SDK Speech Devices – Service Speech
titleSuffix: Azure Cognitive Services
description: Démarrez avec le kit SDK Speech Devices. Le service Speech fonctionne avec une large gamme d’appareils et de sources audio. Le SDK Speech Devices est une bibliothèque préconfigurée qui est associée à des kits de développement de réseau de microphones sur mesure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 7f6793cab3d2d7cc54252bae3a7393f836256bdd
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815535"
---
# <a name="about-the-speech-devices-sdk"></a>À propos du kit de développement logiciel (SDK) de dispositifs vocaux

Le [service Speech](overview.md) fonctionne avec une large gamme d’appareils et de sources audio. Vous pouvez maintenant faire passer vos applications de reconnaissance vocale au niveau supérieur en y associant du matériel et des logiciels. Le SDK Speech Devices est une bibliothèque préconfigurée qui est associée à des kits de développement de réseau de microphones sur mesure.

Le SDK Speech Devices vous permet d’effectuer les opérations suivantes :

- Tester rapidement de nouveaux scénarios de voix.
- Intégrer plus facilement le service Speech basé sur le cloud à votre appareil.
- Créer une expérience utilisateur exceptionnelle pour vos clients.

Le SDK Speech Devices consomme le [SDK Speech](speech-sdk.md). Il l’utilise pour envoyer l’audio qui est traité par notre algorithme de traitement audio avancé du réseau de microphones de l’appareil vers le [service Speech](overview.md). Il utilise l’audio multicanal pour fournir une [reconnaissance vocale](speech-to-text.md) à champ lointain plus précise, par le biais de la suppression du bruit, l’annulation de l’écho, la formation de faisceaux et la déréverbération.

Vous pouvez également utiliser le Kit de développement logiciel (SDK) des appareils Speech pour créer des appareils ambiants disposant de votre propre [mot clé personnalisé](speech-devices-sdk-create-kws.md), afin que la pile qui déclenche l’interaction utilisateur soit propre à votre marque.

Le kit de développement logiciel (SDK) des appareils Speech facilite différents scénarios vocaux, tels que les [assistants vocaux](https://aka.ms/bots/speech/va), les systèmes de commande au volant, la [transcription de conversation](conversation-transcription-service.md) et les haut-parleurs intelligents. Vous pouvez répondre aux utilisateurs avec du texte, leur répondre en utilisant la voix par défaut ou une [voix personnalisée](how-to-customize-voice-font.md), fournir des résultats de recherche, [traduire](speech-translation.md) dans d’autres langues, et bien plus encore. Nous avons hâte de découvrir vos créations !

## <a name="get-the-speech-devices-sdk"></a>Obtenir le Kit de développement logiciel (SDK) de dispositifs vocaux

### <a name="android"></a>Android

Pour des appareils Android, téléchargez la dernière version du [SDK Speech Devices Android](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Pour Windows, l’exemple d’application est fourni en tant qu’application Java multiplateforme. Téléchargez la dernière version du [SDK Speech Devices JRE](https://aka.ms/sdsdk-download-JRE).
L’application est créée avec le package du SDK Speech et l’IDE Eclipse Java (v4) sur Windows 64 bits. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

### <a name="linux"></a>Linux

Pour Linux, l’exemple d’application est fourni en tant qu’application Java multiplateforme. Téléchargez la dernière version du [SDK Speech Devices JRE](https://aka.ms/sdsdk-download-JRE).
L’application est créée avec le package du SDK Speech et l’IDE Eclipse Java (v4) sur Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Choisir votre appareil vocal](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
