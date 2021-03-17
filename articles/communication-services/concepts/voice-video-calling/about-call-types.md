---
title: Concepts vocaux et vidéo dans Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les types d’appels dans Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: eea54be9d159c6aacbbc24e2fe8edf874f8e2d83
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492334"
---
# <a name="voice-and-video-concepts"></a>Concepts vocaux et vidéo

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Vous pouvez utiliser Azure Communication Services pour effectuer ou recevoir des appels un à un ou des appels vocaux et vidéos de groupe. Vos appels peuvent être dirigés vers d’autres appareils connectés à Internet et vers des téléphones traditionnels. Vous pouvez utiliser les bibliothèques de client JavaScript, Android ou iOS de Communication Services pour créer des applications qui permettent à vos utilisateurs de parler entre eux dans le cadre de conversations privées ou de discussions de groupe. Azure Communication Services prend en charge les appels vers et depuis des services ou des bots.

## <a name="call-types-in-azure-communication-services"></a>Types d’appels dans Azure Communication Services

Plusieurs types d’appels peuvent être effectués dans Azure Communication Services. Le type d’appels que vous effectuez détermine votre schéma de signalisation, les flux de trafic de contenu multimédia et le modèle tarifaire.

### <a name="voice-over-ip-voip"></a>Voix sur IP (VoIP)

Lorsqu’un utilisateur de votre application appelle un autre utilisateur de votre application via une connexion Internet ou une connexion de données, l’appel est effectué par le bais de la voix sur IP (VoIP). Dans ce cas, la signalisation et le contenu multimédia transitent par Internet.

### <a name="public-switched-telephone-network-pstn"></a>Réseau téléphonique public commuté (RTPC)

Chaque fois que vos utilisateurs interagissent avec un numéro de téléphone traditionnel, les appels sont facilités par les appels vocaux RTPC (réseau téléphonique public commuté). Pour émettre et recevoir des appels RTPC, vous devez ajouter des fonctionnalités de téléphonie à votre ressource Azure Communication Services. Dans ce cas, la signalisation et le contenu multimédia utilisent une combinaison de technologies basées sur IP et RTPC pour connecter vos utilisateurs.

### <a name="one-to-one-call"></a>Appel un-à-un

Un appel un-à-un sur Azure Communication Services se produit lorsque l’un de vos utilisateurs se connecte à un autre utilisateur en utilisant l’une de nos bibliothèques de client. L’appel peut être de type VoIP ou RTPC.

### <a name="group-call"></a>Appel de groupe

Un appel de groupe sur Azure Communication Services se produit lorsque trois participants ou plus se connectent les uns aux autres. Toute combinaison d’utilisateurs connectés par VoIP et RTPC peut être présente dans le cadre d’un appel de groupe. Un appel un-à-un peut être converti en appel de groupe par l’ajout de participants à l’appel. L’un de ces participants peut être un bot.

### <a name="supported-video-standards"></a>Normes vidéo prises en charge
Nous prenons en charge H.264 (MPEG-4)

### <a name="video-quality"></a>Qualité vidéo
Nous prenons en charge la norme Full HD 1080p sur les kits SDK natifs (iOS, Android). Pour le kit SDK Web (JS), nous prenons en charge la norme HD 720p. La qualité dépend de la bande passante disponible.

### <a name="rooms-concept"></a>Concept des salles
Les salles correspondent à un ensemble d’API et de kits SDK qui vous permettent d’ajouter facilement des interactions audio, vidéo, de partage d’écran, RTPC et SMS à votre site web ou application native.
Dans cette préversion, vous pouvez utiliser l’ID de groupe pour rejoindre la même conversation. Vous pouvez créer autant d’ID de groupe que nécessaire et séparer les utilisateurs en « salles ». À l’avenir, plus de contrôles seront introduits autour des « salles ».

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec les appels](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Pour plus d’informations, consultez les articles suivants :
- Se familiariser avec les [flux d’appels](../call-flows.md) généraux
- [Types de numéro de téléphone](../telephony-sms/plan-solution.md)
- Découvrir les [fonctionnalités des bibliothèques de client d’appel](../voice-video-calling/calling-sdk-features.md)
