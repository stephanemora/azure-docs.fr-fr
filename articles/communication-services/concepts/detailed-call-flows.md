---
title: Topologies de flux d’appels dans Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les topologies de flux d’appels dans Azure Communication Services.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 12/11/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5ffc655cebb19b755b990171ab662576ac541793
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174289"
---
# <a name="call-flow-topologies"></a>Topologies des flux d’appels
Cet article décrit les topologies de flux d’appels Azure Communication Services. Si vous êtes un client d’entreprise intégrant Communication Services dans un réseau que vous gérez, vous avez tout intérêt à lire cet article. Pour une présentation des flux d’appels Communication Services, consultez la [documentation conceptuelle sur les flux d’appels](./call-flows.md).

## <a name="background"></a>Arrière-plan

### <a name="network-concepts"></a>Concepts de réseau

Avant de passer en revue les topologies de flux d’appels, nous allons définir certains termes mentionnés dans le document.

Un **réseau client** contient les segments réseau que vous gérez. Cela peut englober les réseaux câblés et sans fil situés dans votre bureau ou entre plusieurs bureaux, centres de données et fournisseurs de services Internet.

Un réseau client est généralement constitué de plusieurs périmètres réseau avec des pare-feu et/ou des serveurs proxy qui appliquent les stratégies de sécurité de votre organisation. Nous vous recommandons de procéder à une [évaluation complète du réseau](/microsoftteams/3-envision-evaluate-my-environment) pour optimiser les performances et la qualité de votre solution de communication.

Le **réseau Communication Services** est le segment réseau qui prend en charge Azure Communication Services. Ce réseau est géré par Microsoft et est distribué dans le monde entier avec des périphéries proches de la plupart des réseaux client. Ce réseau est responsable du relais de transport, du traitement multimédia pour les appels de groupe et d’autres composants qui prennent en charge les communications multimédias riches et en temps réel.

### <a name="types-of-traffic"></a>Types de trafic

Communication Services repose principalement sur deux types de trafic : **multimédia en temps réel** et **signalisation**.

Le trafic **multimédia en temps** est transmis à l’aide du protocole RTP (Real-Time Transport Protocol). Ce protocole prend en charge la transmission de données de partage audio, vidéo et écran. Ces données sont sensibles aux problèmes de latence réseau. Même s’il est possible de transmettre du contenu multimédia en temps réel à l’aide des protocoles TCP ou HTTP, nous vous recommandons d’utiliser UDP en guise de protocole de la couche transport pour prendre en charge les expériences utilisateur final à hautes performances. Les charges utiles multimédias transmises via RTP sont sécurisées avec SRTP.

Les utilisateurs de votre solution Communication Services se connecteront à vos services à partir de leurs appareils clients. La communication entre ces appareils et vos serveurs est gérée avec la **signalisation**. Par exemple : le lancement d’appel et la conversation en temps réel sont pris en charge par la signalisation entre les appareils et votre service. Si le trafic de signalisation utilise essentiellement HTTPS REST, dans certains scénarios, SIP peut être utilisé comme protocole de trafic de signalisation. Bien que ce type de trafic soit moins sensible à la latence, la signalisation à faible latence offrira aux utilisateurs de votre solution une agréable expérience utilisateur final.

### <a name="interoperability-restrictions"></a>Restrictions d’interopérabilité

Le flux multimédia transitant par Communication Services est limité comme suit :

**Relais multimédias tiers.** L’interopérabilité avec les relais multimédias tiers n’est pas prise en charge. Si l’un de vos points de terminaison multimédias est Communication Services, votre contenu multimédia ne peut transiter que par des relais multimédias natifs Microsoft, y compris ceux qui prennent en charge Microsoft Teams et Skype Entreprise.

Un contrôleur de frontière de session (SBC) tiers situé à la limite du réseau téléphonique commuté doit mettre fin au flux RTP/RTCP, sécurisé à l’aide de SRTP, et ne pas le relayer au tronçon suivant. Si vous relayez le flux au tronçon suivant, il est possible qu’il ne soit pas compris.

**Serveurs proxy SIP tiers.** Un dialogue SIP de signalisation Communication Services avec un SBC tiers et/ou une passerelle peut transiter par des proxys SIP natifs Microsoft (tout comme Teams). L’interopérabilité avec des proxys SIP tiers n’est pas prise en charge.

**B2BUA (ou SBC) tiers.** Un flux multimédia Communication Services vers et en provenance du réseau téléphonique commuté est terminé par un SBC tiers. L’interopérabilité avec un SBC tiers au sein du réseau Communication Services (où un SBC tiers sert de médiateur entre deux points de terminaison Communication Services) n’est pas prise en charge.

### <a name="unsupported-technologies"></a>Technologies non prises en charge

**Réseau VPN.** Communication Services ne prend pas en charge la transmission multimédia sur les réseaux privés virtuels (VPN). Si vos utilisateurs utilisent des clients VPN, le client doit fractionner et router le trafic multimédia sur une connexion non VPN, comme indiqué dans l’article [Enabling Lync media to bypass a VPN tunnel](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210).

*Remarque. Même si le titre de cet article indique Lync, son contenu est applicable à Azure Communication Services et Teams.*

**Modélisateurs de paquets.** Les dispositifs de capture, d’inspection ou de modélisation de paquets ne sont pas pris en charge et peuvent altérer considérablement la qualité.

### <a name="call-flow-principles"></a>Principes du flux d’appels

Il existe quatre principes généraux qui sous-tendent les flux d’appels Communication Services :

* **Le premier participant à un appel de groupe Communication Services détermine la région dans laquelle l’appel est hébergé**. Il existe des exceptions à cette règle dans certaines topologies, qui sont décrites ci-après.
* **Le point de terminaison multimédia utilisé pour prendre en charge un appel Communication Services est sélectionné en fonction des besoins de traitement multimédia** et n’est pas affecté par le nombre de personnes qui participent à un appel. Par exemple, un appel point à point peut utiliser un point de terminaison multimédia dans le cloud pour traiter le contenu multimédia en vue de sa transcription ou de son enregistrement, tandis qu’un appel à deux participants peut ne pas utiliser de point de terminaison multimédia. Les appels de groupe utilisent un point de terminaison multimédia à des fins de mixage et de routage. Ce point de terminaison est sélectionné en fonction de la région dans laquelle l’appel est hébergé. Le trafic multimédia envoyé par un client au point de terminaison multimédia peut être routé directement ou il peut utiliser un relais de transport dans Azure si les restrictions du pare-feu du réseau client l’exigent.
* **Le trafic multimédia pour les appels d’homologue à homologue emprunte la route la plus directe possible**, partant du principe que l’appel n’a pas besoin d’un point de terminaison multimédia dans le cloud. La route préférée est celle qui mène directement à l’homologue distant (client). Si aucune route directe n’est disponible, un ou plusieurs relais de transport relaient le trafic. Le trafic multimédia ne doit pas transiter par les serveurs qui jouent le rôle de modélisateurs de paquets, de serveurs VPN ou qui remplissent d’autres fonctions susceptibles de retarder le traitement et de dégrader l’expérience utilisateur final.
* **Le trafic de signalisation se dirige toujours vers le serveur le plus proche de l’utilisateur.**

Pour en savoir plus sur le choix du chemin multimédia, consultez la [documentation conceptuelle sur les flux d’appels](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Flux d’appels dans plusieurs topologies

### <a name="communication-services-internet"></a>Communication Services (Internet)

Cette topologie est utilisée par les clients qui utilisent Communication Services à partir du cloud sans aucun déploiement local, tel que l’interface SIP. Dans cette topologie, le trafic à destination et en provenance de Communication Services transite par Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Topologie Azure Communication Services.":::

*Figure 1 – Topologie Communication Services*

La direction indiquée par les flèches dans le diagramme ci-dessus reflète la direction de lancement de la communication qui affecte la connectivité au niveau des périmètres de l’entreprise. Dans le cas du protocole UDP pour le contenu multimédia, le ou les premiers paquets peuvent circuler dans la direction inverse, mais ces paquets peuvent être bloqués tant que les paquets qui vont dans l’autre direction sont en circulation.

Description des flux :
* Flux 2* – Représente un flux initié par un utilisateur sur le réseau client à destination d’Internet au titre de l’expérience Communication Services de l’utilisateur. La transmission multimédia d’homologue à homologue et DNS sont des exemples de ce type de flux.
* Flux 2 – Représente un flux initié par un utilisateur Communication Services mobile distant avec un VPN à destination du réseau client.
* Flux 3 – Représente un flux initié par un utilisateur Communication Services mobile distant à destination de points de terminaison Communication Services.
* Flux 4 – Représente un flux initié par un utilisateur sur le réseau client vers Communication Services.
* Flux 5 – Représente un flux multimédia d’homologue à homologue entre un utilisateur Communication Services et un autre utilisateur au sein du réseau client.
* Flux 6 – Représente un flux multimédia d’homologue à homologue entre un utilisateur Communication Services mobile distant et un autre utilisateur Communication Services mobile distant via Internet.

### <a name="use-case-one-to-one"></a>Cas d’usage Un à un

Les appels un-à-un utilisent un modèle commun selon lequel l’appelant obtient un ensemble de candidats constitués par des adresses IP/ports, comprenant des candidats locaux, relais et réflexifs (adresse IP publique du client telle que la voit le relais). L’appelant envoie ces candidats au tiers appelé ; le tiers appelé obtient également un ensemble similaire de candidats et les envoie à l’appelant. Des messages de vérification de la connectivité STUN sont utilisés pour identifier les chemins multimédias de la partie appelant/tiers qui fonctionnent, et c’est celui qui fonctionne le mieux qui est sélectionné. Le contenu multimédia (c’est-à-dire des paquets RTP/RTCP sécurisés avec SRTP) est ensuite envoyé en utilisant la paire de candidats sélectionnée. Le relais de transport est déployé au titre d’Azure Communication Services.

Si les candidats locaux à adresse IP/port ou les candidats réflexifs sont connectés, le chemin direct entre les clients (ou en utilisant une traduction d’adresses réseau) est sélectionné pour le contenu multimédia. Si les clients se trouvent tous deux sur le réseau client, le chemin direct doit être sélectionné. Pour cela, le réseau client doit disposer d’une connectivité UDP directe. Si les clients sont tous deux des utilisateurs cloud nomades, selon la traduction d’adresses réseau ou le pare-feu, le contenu multimédia peut utiliser une connectivité directe.

Si un client est interne au réseau client et qu’un autre est externe (par exemple, un utilisateur cloud mobile), il est peu probable que la connectivité directe entre les candidats locaux ou réflexifs fonctionne. Dans ce cas, une solution consiste à utiliser l’un des candidats de relais de transport de l’un ou l’autre des clients (par exemple, le client interne a obtenu un candidat relais à partir du relais de transport dans Azure ; le client externe doit pouvoir envoyer des paquets STUN/RTP/RTCP au relais de transport). Une autre solution consiste pour le client interne à envoyer au candidat relais obtenu par le client cloud mobile. Même si la connectivité UDP est vivement recommandée pour le contenu multimédia, TCP est pris en charge.

**Procédure générale :**
1.  L’utilisateur A Communication Services résout le nom de domaine de l’URL (DNS) en utilisant le flux 2.
2.  L’utilisateur A alloue un port de relais multimédia sur le relais de transport Teams en utilisant le flux 4.
3.  L’utilisateur A Communication Services envoie à Communication Services une « invitation » avec des candidats ICE en utilisant le flux 4.
4.  Communication Services notifie l’utilisateur B en utilisant le flux 4.
5.  L’utilisateur B alloue un port de relais multimédia sur le relais de transport Teams en utilisant le flux 4.
6.  L’utilisateur B envoie une « réponse » avec des candidats ICE en utilisant le flux 4, qui est renvoyée à l’utilisateur A en utilisant le flux 4.
7.  L’utilisateur A et l’utilisateur B appellent les tests de connectivité ICE et le meilleur chemin multimédia disponible est sélectionné (consultez les diagrammes ci-dessous pour différents cas d’usage).
8.  Les deux utilisateurs envoient des données de télémétrie à Communication Services en utilisant le flux 4.

### <a name="customer-network-intranet"></a>Réseau client (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Flux de trafic au sein du réseau client.":::

*Figure 2 – Au sein du réseau client*

À l’étape 7, le flux 5 de transmission multimédia d’homologue à homologue est sélectionné.

Cette transmission multimédia est bidirectionnelle. La direction du flux 5 indique qu’une partie initie la communication dans une optique de connectivité. Dans ce cas, la direction empruntée n’a pas d’importance, car les deux points de terminaison se trouvent dans le réseau client.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Du réseau client vers un utilisateur externe (transmission multimédia relayée par le relais de transport Teams)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Flux d’appel un-à-un via un relais.":::

*Figure 3 – Du réseau client vers un utilisateur externe (transmission multimédia relayée par le relais de transport Azure)*

À l’étape 7, le flux 4 (du réseau du client vers Communication Services) et le flux 3 (d’un utilisateur Communication Services mobile distant vers Azure Communication Services) sont sélectionnés.

Ces flux sont relayés par le relais de transport Teams dans Azure.

Cette transmission multimédia est bidirectionnelle. La direction indique la partie qui est à l’origine de la communication dans une optique de connectivité. Dans ce cas, ces flux sont utilisés pour la signalisation et la transmission multimédia, avec différents protocoles de transport et différentes adresses.

### <a name="customer-network-to-external-user-direct-media"></a>Du réseau client vers un utilisateur externe (transmission multimédia directe)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Flux d’appel un-à-un avec un utilisateur externe.":::

*Figure 4 – Du réseau client vers un utilisateur externe (transmission multimédia directe)*

À l’étape 7, le flux 2 (du réseau client vers l’homologue du client via Internet) est sélectionné.

La transmission multimédia directe avec un utilisateur mobile distant (non relayée par Azure) est facultative. En d’autres termes, vous pouvez bloquer ce chemin pour imposer un chemin multimédia qui transite par un relais de transport dans Azure.

Cette transmission multimédia est bidirectionnelle. La direction du flux 2 vers l’utilisateur mobile distant indique qu’une partie initie la communication dans une optique de connectivité.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>D’un utilisateur VPN vers un utilisateur interne (transmission multimédia relayée par le relais de transport Teams)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Flux d’appel un-à-un avec un utilisateur VPN transitant par un relais.":::

*Figure 5 – D’un utilisateur VPN vers un utilisateur interne (transmission multimédia relayée par Azure Relay)*

La signalisation entre le VPN et le réseau client utilise le flux 2*. La signalisation entre le réseau client et Azure utilise le flux 4. Cependant, la transmission multimédia contourne le VPN et est routée en utilisant les flux 3 et 4 via le relais multimédia Azure.

### <a name="vpn-user-to-internal-user-direct-media"></a>D’un utilisateur VPN vers un utilisateur interne (transmission multimédia directe)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Flux d’appel un-à-un (utilisateur interne) avec un utilisateur VPN et une transmission multimédia directe":::

*Figure 6 – D’un utilisateur VPN vers un utilisateur interne (transmission multimédia directe)*

La signalisation entre le VPN et le réseau client utilise le flux 2'. La signalisation entre le réseau client et Azure utilise le flux 4. Cependant, la transmission multimédia contourne le VPN et est routée en utilisant le flux 2 du réseau client vers Internet.

Cette transmission multimédia est bidirectionnelle. La direction du flux 2 vers l’utilisateur mobile distant indique qu’une partie initie la communication dans une optique de connectivité.

### <a name="vpn-user-to-external-user-direct-media"></a>D’un utilisateur VPN vers un utilisateur externe (transmission multimédia directe)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Flux d’appel un-à-un (utilisateur externe) avec un utilisateur VPN et une transmission multimédia directe":::

*Figure 7 – D’un utilisateur VPN vers un utilisateur externe (transmission multimédia directe)*

La signalisation entre l’utilisateur VPN et le réseau client utilise le flux 2* et le flux 4 vers Azure. Cependant, la transmission multimédia contourne le VPN et est routée en utilisant le flux 6.

Cette transmission multimédia est bidirectionnelle. La direction du flux 6 vers l’utilisateur mobile distant indique qu’une partie initie la communication dans une optique de connectivité.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Cas d’usage : Du client Communication Services vers le réseau téléphonique commuté via une jonction Communication Services

Communication Services permet de passer et recevoir des appels sur le réseau téléphonique commuté public (RTPC). Si la jonction RTPC est connectée avec des numéros de téléphone fournis par Communication Services, il n’y a pas de conditions de connectivité particulières pour ce cas d’usage. Si vous souhaitez connecter votre propre jonction RTPC locale à Azure Communication Services, vous pouvez utiliser l’interface SIP (disponible dans CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Appel un-à-un avec un participant RTPC":::

*Figure 8 – D’un utilisateur Communication Services vers le RTPC via un jonction Azure*

Dans ce cas, la signalisation et la transmission multimédia du réseau client vers Azure utilisent le flux 4.

### <a name="use-case-communication-services-group-calls"></a>Cas d’usage Appels de groupe Communication Services

Le service de partage audio/vidéo/d’écran (VBSS) fait partie intégrante d’Azure Communication Services. Il possède une adresse IP publique qui doit être accessible à partir du réseau client et à partir d’un client cloud nomade. Chaque client/point de terminaison doit pouvoir se connecter au service.

Les clients internes obtiennent des candidats locaux, réflexifs et relais de la même manière que pour les appels un-à-un. Les clients envoient ces candidats au service dans une invitation. Le service n’utilise pas de relais, car il possède une adresse IP accessible publiquement. De ce fait, il répond avec son candidat local à adresse IP. Le client et le service vérifient la connectivité de la même manière que pour les appels un-à-un.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="Appel de groupe OACS":::

*Figure 9 – Appels de groupe Communication Services*

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Prise en main des appels](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Les documents suivants peuvent vous intéresser :

- En savoir plus sur les [types d’appels](../concepts/voice-video-calling/about-call-types.md)
- En savoir plus sur l’[architecture client-serveur](./client-and-server-architecture.md)