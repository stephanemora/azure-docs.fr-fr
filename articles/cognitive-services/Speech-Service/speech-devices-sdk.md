---
title: À propos du kit de développement logiciel (SDK) de dispositifs vocaux
titleSuffix: Azure Cognitive Services
description: Consultez une présentation du SDK Speech Devices.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: erhopf
ms.openlocfilehash: eac3542059f1bc5d32a91ef871e5185fad1d2798
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49464095"
---
# <a name="about-the-speech-devices-sdk-preview"></a>À propos du kit de développement logiciel (SDK) Speech Devices (préversion)

Le [service Speech](overview.md) fonctionne avec une large gamme d’appareils et de sources audio. Vous pouvez maintenant faire passer vos applications de reconnaissance vocale au niveau supérieur en y associant du matériel et des logiciels. Le SDK Speech Devices est une bibliothèque préconfigurée qui est associée à des kits de développement de réseau de microphones sur mesure. 

Le SDK Speech Devices vous permet d’effectuer les opérations suivantes :
* Tester rapidement de nouveaux scénarios de voix.
* Intégrer plus facilement le service Speech basé sur le cloud à votre appareil.
* Créer une expérience utilisateur exceptionnelle pour vos clients. 

Le SDK Speech Devices consomme le [SDK Speech](speech-sdk.md). Il l’utilise pour envoyer l’audio qui est traité par notre algorithme de traitement audio avancé du réseau de microphones de l’appareil vers le [service Speech](overview.md). Il utilise l’audio multicanal pour fournir une [reconnaissance vocale](speech-to-text.md) à champ lointain plus précise, par le biais de la suppression du bruit, l’annulation de l’écho, la formation de faisceaux et la déréverbération.

Vous pouvez également utiliser le SDK Speech Devices pour créer des appareils ambiants disposant de votre propre [mot déclencheur personnalisé](speech-devices-sdk-create-kws.md), afin que le signal qui déclenche l’interaction utilisateur soit propre à votre marque. 

Le SDK Speech Devices facilite différents scénarios vocaux, tels que l’utilisation de systèmes de commande au volant, d’assistants en magasin ou à domicile, et de haut-parleurs intelligents. Vous pouvez répondre aux utilisateurs avec du texte, leur répondre en utilisant la voix par défaut ou une [voix personnalisée](how-to-customize-voice-font.md), fournir des résultats de recherche, [traduire](speech-translation.md) dans d’autres langues, et bien plus encore. Nous avons hâte de découvrir vos créations !

## <a name="development-kit-providers"></a>Fournisseurs de kits de développement

Actuellement, les conceptions de référence du système de bout en bout complètes suivantes sont disponibles : 

|||
|-|-|
|[![Logo ROOBO](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO fournit des solutions de système d’intelligence artificielle (IA) complètes pour les appareils électroménagers, les automobiles, les robots, les jouets et d’autres secteurs d’activité. Les conceptions de référence de ROOBO réduisent considérablement les délais de commercialisation via l’intégration au service Microsoft Speech. [Visitez ROOBO](http://ddk.roobo.com/).|

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, créez un [compte Azure gratuit](https://azure.microsoft.com/free/ai/) et inscrivez-vous pour obtenir le kit de développement logiciel (SDK) Speech Devices.

> [!div class="nextstepaction"]
> [S’inscrire pour le kit de développement logiciel (SDK) Speech Devices](get-speech-devices-sdk.md)

