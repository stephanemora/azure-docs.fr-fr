---
title: Qu’est-ce qu’Azure Communication Services ?
description: Découvrez comment Azure Communication Services vous aide à développer de riches expériences utilisateur avec des communications en temps réel.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588103"
---
# <a name="what-is-azure-communication-services"></a>Qu’est-ce qu’Azure Communication Services ?

Azure Communication Services vous permet d’ajouter facilement des communications en temps réel de nature vocale, vidéo et téléphonique à vos applications. Les kits SDK Communication Services vous donnent également la possibilité d’ajouter des fonctionnalités SMS à vos solutions de communication. La solution Azure Communication Services est indépendante des identités, et vous détenez le contrôle total sur la façon dont les utilisateurs finals sont identifiés et authentifiés. Vous pouvez connecter des êtres humains aux services (bots) ou au plan de données de communication.

Les applications incluent :

- **Entreprise-client (B2C).** Les employés et services d’une entreprise peuvent interagir avec ses clients via des conversations vocales, vidéo et texte dans une application mobile ou un navigateur personnalisé. Une organisation peut envoyer et recevoir des SMS, ou utiliser un système de réponse vocale interactive (RVI) au moyen d’un numéro de téléphone qui est obtenu via Azure. L’[intégration à Microsoft Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md) permet aux clients de participer aux réunions Teams hébergées par les employés ; elle constitue une solution idéale pour les scénarios à distance dans les secteurs de la santé, de la banque et de l’assistance produit, sachant que les employés sont peut-être déjà familiarisés avec Teams.
- **Consommateur-consommateur.** Créez des espaces sociaux attrayants permettant l’interaction entre les consommateurs avec des conversations vocales, vidéo et texte. N’importe quel type d’interface utilisateur peut être élaboré sur les kits SDK Azure Communication Services, sinon des exemples complets d’applications et des ressources d’interface utilisateur sont disponibles pour vous mettre rapidement le pied à l’étrier.

Pour plus d’informations, consultez notre [vidéo Microsoft Mechanics](https://www.youtube.com/watch?v=apBX7ASurgM) ou les ressources liées ci-dessous.

## <a name="common-scenarios"></a>Scénarios courants

<br>

| Ressource                               |Description                           |
|---                                    |---                                   |
|**[Créer une ressource Communication Services](./quickstarts/create-communication-resource.md)**|Commencez votre utilisation d’Azure Communication Services en vous servant du portail Azure ou du kit SDK Communication Services, et provisionnez votre première ressource Communication Services. Une fois que vous disposez de la chaîne de connexion de votre ressource Communication Services, vous pouvez provisionner vos premiers jetons d’accès utilisateur.|
|**[Obtenir un numéro de téléphone](./quickstarts/telephony-sms/get-phone-number.md)**|Vous pouvez utiliser Azure Communication Services pour provisionner et diffuser des numéros de téléphone. Ces numéros de téléphone peuvent être utilisés pour appeler ou recevoir des appels téléphoniques, et créer des solutions SMS.|
|**[Envoyer un SMS à partir de votre application](./quickstarts/telephony-sms/send.md)**|Le kit SDK SMS Azure Communication Services est utilisé pour envoyer et recevoir des SMS depuis des applications de service.|

Dès la création d’une ressource Communication Services effectuée, vous pouvez commencer à générer des scénarios clients, tels que des appels vocaux et vidéo ou une conversation par écrit :

| Ressource                               |Description                           |
|---                                    |---                                   |
|**[Créer votre premier jeton d’accès utilisateur](./quickstarts/access-tokens.md)**|Les jetons d’accès utilisateur servent à authentifier les clients auprès de votre ressource Azure Communication Services. Ces jetons sont provisionnés et réémis à l’aide du kit SDK Communication Services.|
|**[Bien démarrer avec l’appel vocal et vidéo](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services vous permet d’ajouter des appels vocaux et vidéo à votre navigateur ou à vos applications natives avec le kit SDK Appel. |
|**[Joindre votre application d’appel à une réunion Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services peut être utilisé pour créer des expériences de réunion personnalisées qui interagissent avec Microsoft Teams. Les utilisateurs de vos solutions Communication Services peuvent interagir avec des participants Teams via l’audio, la vidéo, la conversation et le partage d’écran.|
|**[Bien démarrer avec les conversations](./quickstarts/chat/get-started.md)**|Le kit SDK Conversation Azure Communication Services est utilisé pour ajouter la conversation texte en temps réel à vos applications.|

## <a name="samples"></a>Exemples

Les exemples suivants illustrent l’utilisation de bout en bout d’Azure Communication Services. Servez-vous de ces exemples pour démarrer vos propres solutions Communication Services.
<br>

| Nom d’exemple                               | Description                           |
|---                                    |---                                   |
|**[Exemple de bannière d’appel de groupe](./samples/calling-hero-sample.md)**| Téléchargez un exemple d’application conçu pour l’appel de groupe sur les navigateurs ainsi que sur les appareils iOS et Android. |
|**[Exemple de bannière de conversation de groupe](./samples/chat-hero-sample.md)**| Téléchargez un exemple d’application conçu pour la conversation texte de groupe sur les navigateurs. |


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
