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
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: cb0e079eee78f865d472b55b162fb65b5eadaf44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460443"
---
# <a name="about-the-speech-devices-sdk-preview"></a>À propos du kit de développement logiciel (SDK) Speech Devices (préversion)

Le [Services Speech](overview.md) fonctionnent avec un large éventail de périphériques et sources de l’audio. Vous pouvez maintenant faire passer vos applications de reconnaissance vocale au niveau supérieur en y associant du matériel et des logiciels. Le SDK Speech Devices est une bibliothèque préconfigurée qui est associée à des kits de développement de réseau de microphones sur mesure.

Le SDK Speech Devices vous permet d’effectuer les opérations suivantes :
* Tester rapidement de nouveaux scénarios de voix.
* Intégrer plus facilement les Services de reconnaissance vocale basés sur le cloud dans votre appareil.
* Créer une expérience utilisateur exceptionnelle pour vos clients.

Le SDK Speech Devices consomme le [SDK Speech](speech-sdk.md). Elle utilise le Speech SDK pour envoyer l’audio traité par notre algorithme avancées de traitement audio à partir du tableau de microphone de l’appareil à la [Services Speech](overview.md). Il utilise l’audio multicanal pour fournir une [reconnaissance vocale](speech-to-text.md) à champ lointain plus précise, par le biais de la suppression du bruit, l’annulation de l’écho, la formation de faisceaux et la déréverbération.

Vous pouvez également utiliser le SDK Speech Devices pour créer des appareils ambiants disposant de votre propre [mot déclencheur personnalisé](speech-devices-sdk-create-kws.md), afin que le signal qui déclenche l’interaction utilisateur soit propre à votre marque.

Le SDK Speech Devices facilite différents scénarios vocaux, tels que l’utilisation de systèmes de commande au volant, d’assistants en magasin ou à domicile, et de haut-parleurs intelligents. Vous pouvez répondre aux utilisateurs avec du texte, leur répondre en utilisant la voix par défaut ou une [voix personnalisée](how-to-customize-voice-font.md), fournir des résultats de recherche, [traduire](speech-translation.md) dans d’autres langues, et bien plus encore. Nous avons hâte de découvrir vos créations !

## <a name="development-kit-providers"></a>Fournisseurs de kits de développement

Actuellement, les conceptions de référence du système de bout en bout complètes suivantes sont disponibles :

|||
|-|-|
|[![Logo ROOBO](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO fournit des solutions de système d’intelligence artificielle (IA) complètes pour les appareils électroménagers, les automobiles, les robots, les jouets et d’autres secteurs d’activité. Conceptions de référence de ROOBO réduisent considérablement le développement temps-to-market via l’intégration avec les Services de reconnaissance vocale de Azure. [Visitez ROOBO](http://ddk.roobo.com/).|

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, créez un [compte Azure gratuit](https://azure.microsoft.com/free/ai/) et inscrivez-vous pour obtenir le kit de développement logiciel (SDK) Speech Devices.

> [!div class="nextstepaction"]
> [S’inscrire pour le kit de développement logiciel (SDK) Speech Devices](get-speech-devices-sdk.md)
