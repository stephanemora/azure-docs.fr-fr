---
title: Qu’est-ce qu’Azure Communication Services ?
description: Découvrez comment Azure Communication Services vous aide à développer de riches expériences utilisateur avec des communications en temps réel.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: da099fe09b44a8c7aeecdfd7118e936ce3f0ff91
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113113064"
---
# <a name="what-is-azure-communication-services"></a>Qu’est-ce qu’Azure Communication Services ?

Azure Communication Services regroupe des services cloud dotés d’API REST et de SDK de bibliothèque de client qui vous aident à intégrer la communication à vos applications. Vous pouvez ajouter des fonctionnalités de communication à vos applications sans être expert en technologies de communication, telles que l’encodage multimédia et la mise en réseau en temps réel. Azure Communication Services prend en charge différents formats de communication :

1. Appel vocal et appel vidéo
1. Conversation de texte enrichi
1. sms

Les applications d’appel vocal et vidéo peuvent interagir avec le réseau téléphonique commuté (RTPC) public. Vous pouvez obtenir des numéros de téléphone directement via les API REST, les kits de développement logiciel (SDK) Azure Communication Services ou le portail Azure. Le routage direct d’Azure Communication Services vous permet d’utiliser des contrôleurs SIP et de frontière de session pour connecter vos propres opérateurs RTPC et apporter vos propres numéros de téléphone.

En plus des API REST, les [bibliothèques clientes Azure Communication Services](./concepts/sdk-options.md) sont disponibles pour différents langages et plateformes, notamment les navigateurs web (JavaScript), iOS (Swift), Java (Android), Windows (.net). La solution Azure Communication Services est indépendante des identités et vous contrôlez intégralement la façon dont les utilisateurs finals sont identifiés et authentifiés.

Les scénarios pour Azure Communication Services sont les suivants :

- **Entreprise-client (B2C).** Les employés et services d’une entreprise interagissent avec ses clients via des conversations vocales, vidéo et texte dans une application mobile ou un navigateur personnalisé. Une organisation peut envoyer et recevoir des SMS, ou [utiliser un système de réponse vocale interactive (RVI)](https://github.com/microsoft/botframework-telephony/blob/main/EnableTelephony.md) au moyen d’un numéro de téléphone qui est obtenu via Azure. L’[intégration à Microsoft Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md) permet de connecter des clients aux réunions Teams hébergées par les employés ; elle constitue une solution idéale pour les scénarios à distance dans les secteurs de la santé, de la banque et de l’assistance produit, sachant que les employés sont peut-être déjà familiarisés avec Teams.
- **Consommateur à consommateur (C2C).** Créez des espaces sociaux attrayants permettant l’interaction entre les consommateurs avec des conversations vocales, vidéo et texte. N’importe quel type d’interface utilisateur peut être élaboré sur les kits SDK Azure Communication Services. Des exemples complets d’applications et des ressources d’interface open source sont disponibles pour vous mettre rapidement le pied à l’étrier.

Pour plus d’informations, consultez notre [vidéo Microsoft Mechanics](https://www.youtube.com/watch?v=apBX7ASurgM) ou les ressources liées ci-dessous.

## <a name="common-scenarios"></a>Scénarios courants

<br>

| Ressource                               |Description                           |
|---                                    |---                                   |
|**[Créer une ressource Communication Services](./quickstarts/create-communication-resource.md)**|Commencez votre utilisation d’Azure Communication Services en vous servant du portail Azure ou du kit SDK Communication Services, et provisionnez votre première ressource Communication Services. Une fois que vous disposez de la chaîne de connexion de votre ressource Communication Services, vous pouvez provisionner vos premiers jetons d’accès utilisateur.|
|**[Obtenir un numéro de téléphone](./quickstarts/telephony-sms/get-phone-number.md)**|Utilisez Azure Communication Services pour approvisionner et diffuser des numéros de téléphone. Ces numéros de téléphone peuvent être utilisés pour appeler ou recevoir des appels téléphoniques, et créer des solutions SMS.|
|**[Envoyer un SMS à partir de votre application](./quickstarts/telephony-sms/send.md)**| Les API et kits de développement logiciel (SDK) Azure Communication Services SMS sont utilisés pour envoyer et recevoir des SMS depuis des applications de service.|

Dès la création d’une ressource Communication Services effectuée, vous pouvez commencer à générer des scénarios clients, tels que des appels vocaux et vidéo ou une conversation par écrit :

| Ressource                               |Description                           |
|---                                    |---                                   |
|**[Créer votre premier jeton d’accès utilisateur](./quickstarts/access-tokens.md)**|Les jetons d’accès utilisateur authentifient les clients auprès de votre ressource Azure Communication Services. Ces jetons sont approvisionnés et réémis à l’aide des API et kits de developpement logiciel (SDK) Communication Services Identity.|
|**[Bien démarrer avec l’appel vocal et vidéo](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services vous permet d’ajouter des appels vocaux et vidéo à votre navigateur ou à vos applications natives avec le kit SDK Appel. |
|**[Ajouter les appels téléphoniques à votre application](./quickstarts/voice-video-calling/pstn-call.md)**|Avec Azure Communication Services, vous pouvez ajouter des fonctionnalités d’appel téléphonique à votre application.|
|**[Joindre votre application d’appel à une réunion Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services peut être utilisé pour créer des expériences de réunion personnalisées qui interagissent avec Microsoft Teams. Les utilisateurs de vos solutions Communication Services peuvent interagir avec des participants Teams via l’audio, la vidéo, la conversation et le partage d’écran.|
|**[Bien démarrer avec les conversations](./quickstarts/chat/get-started.md)**|Le kit SDK Conversation Azure Communication Services est utilisé pour ajouter la conversation texte en temps réel à vos applications.|
|**[Connecter un bot Microsoft à un numéro de téléphone](https://github.com/microsoft/botframework-telephony)**|Le canal de téléphonie est un canal dans Microsoft Bot Framework qui permet au bot d’interagir avec les utilisateurs sur le téléphone. Il tire parti de la puissance de Microsoft Bot Framework associée à Azure Communication Services et aux services de reconnaissance vocale Azure.  |


## <a name="samples"></a>Exemples

Les exemples suivants illustrent l’utilisation de bout en bout d’Azure Communication Services. Servez-vous de ces exemples pour démarrer vos propres solutions Communication Services.
<br>

| Nom d’exemple                               | Description                           |
|---                                    |---                                   |
|**[Exemple de bannière d’appel de groupe](./samples/calling-hero-sample.md)**| Téléchargez un exemple d’application conçu pour l’appel de groupe sur les navigateurs ainsi que sur les appareils iOS et Android. |
|**[Exemple de bannière de conversation de groupe](./samples/chat-hero-sample.md)**| Téléchargez un exemple d’application conçu pour la conversation texte de groupe sur les navigateurs. |
|**[Exemple d’appel web](./samples/web-calling-sample.md)**| Téléchargez un exemple d’application web conçu pour l’appel audio, vidéo et RTC. |


## <a name="platforms-and-sdk-libraries"></a>Plateformes et bibliothèques du SDK

Apprenez-en davantage sur les kits SDK Azure Communication Services avec les ressources ci-dessous. Les API REST sont disponibles pour la plupart des fonctionnalités si vous souhaitez créer vos propres clients, sinon accédez au service via Internet.

| Ressource                               | Description                           |
|---                                    |---                                   |
|**[Bibliothèques du SDK et API REST](./concepts/sdk-options.md)**|Les fonctionnalités Azure Communication Services sont organisées d’un point de vue conceptuel en six domaines, chacun représenté par un SDK. Vous pouvez choisir les bibliothèques du SDK à utiliser en fonction de vos besoins de communication en temps réel.|
|**[Vue d’ensemble du SDK Appel](./concepts/voice-video-calling/calling-sdk-features.md)**|Consultez la vue d’ensemble du SDK Appel Communication Services.|
|**[Vue d’ensemble du SDK Conversation](./concepts/chat/sdk-features.md)**|Consultez la vue d’ensemble du SDK Conversation Communication Services.|
|**[Vue d’ensemble du SDK SMS](./concepts/telephony-sms/sdk-features.md)**|Consultez la vue d’ensemble du SDK SMS Communication Services.|

## <a name="other-microsoft-communication-services"></a>Autres services de communication Microsoft

Vous pouvez envisager d’utiliser deux autres produits de Microsoft Communication qui, à l’heure actuelle, ne sont pas directement interopérables avec Communication Services :

 - Les [API de communication cloud Microsoft Graph](/graph/cloud-communications-concept-overview) permettent aux organisations de créer des expériences de communication liées aux utilisateurs Azure Active Directory disposant de licences Microsoft 365. C’est idéal pour les applications liées à Azure Active Directory ou lorsque vous souhaitez étendre les expériences de productivité dans Microsoft Teams. Il existe également des API pour créer des applications et une personnalisation au sein de l’[expérience Teams](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest).

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) simplifie l’ajout de communications de conversation et de communication de données à faible latence aux jeux. Même si vous pouvez utiliser la technologie Communication Services pour les systèmes de conversation de jeux et de réseau, PlayFab est une option personnalisée et gratuite sur Xbox.


## <a name="next-steps"></a>Étapes suivantes

 - [Créer une ressource Communication Services](./quickstarts/create-communication-resource.md)
