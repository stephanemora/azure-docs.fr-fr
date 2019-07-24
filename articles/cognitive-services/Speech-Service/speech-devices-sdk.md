---
title: À propos du kit SDK Speech Devices - Services Speech
titleSuffix: Azure Cognitive Services
description: Démarrez avec le kit SDK Speech Devices. Les services Speech fonctionnent avec une large gamme d’appareils et de sources audio. Vous pouvez maintenant faire passer vos applications de reconnaissance vocale au niveau supérieur en y associant du matériel et des logiciels. Le SDK Speech Devices est une bibliothèque préconfigurée qui est associée à des kits de développement de réseau de microphones sur mesure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718545"
---
# <a name="about-the-speech-devices-sdk"></a>À propos du kit de développement logiciel (SDK) de dispositifs vocaux

Les [services Speech](overview.md) fonctionnent avec une large gamme d’appareils et de sources audio. Vous pouvez maintenant faire passer vos applications de reconnaissance vocale au niveau supérieur en y associant du matériel et des logiciels. Le SDK Speech Devices est une bibliothèque préconfigurée qui est associée à des kits de développement de réseau de microphones sur mesure.

Le SDK Speech Devices vous permet d’effectuer les opérations suivantes :

* Tester rapidement de nouveaux scénarios de voix.
* Intégrer plus facilement les services Speech basés sur le cloud à votre appareil.
* Créer une expérience utilisateur exceptionnelle pour vos clients.

Le SDK Speech Devices consomme le [SDK Speech](speech-sdk.md). Il l’utilise pour envoyer l’audio qui est traité par notre algorithme de traitement audio avancé du réseau de microphones de l’appareil vers les [services Speech](overview.md). Il utilise l’audio multicanal pour fournir une [reconnaissance vocale](speech-to-text.md) à champ lointain plus précise, par le biais de la suppression du bruit, l’annulation de l’écho, la formation de faisceaux et la déréverbération.

Vous pouvez également utiliser le SDK Speech Devices pour créer des appareils ambiants disposant de votre propre [mot déclencheur personnalisé](speech-devices-sdk-create-kws.md), afin que le signal qui déclenche l’interaction utilisateur soit propre à votre marque.

Le SDK Speech Devices facilite différents scénarios vocaux, tels que les [assistants virtuels « voice first » personnalisés](https://aka.ms/bots/speech/va), les systèmes de commande au volant, la [transcription de conversation](conversation-transcription-service.md) et les haut-parleurs intelligents. Vous pouvez répondre aux utilisateurs avec du texte, leur répondre en utilisant la voix par défaut ou une [voix personnalisée](how-to-customize-voice-font.md), fournir des résultats de recherche, [traduire](speech-translation.md) dans d’autres langues, et bien plus encore. Nous avons hâte de découvrir vos créations !

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
> [Choisir un Speech Device](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
