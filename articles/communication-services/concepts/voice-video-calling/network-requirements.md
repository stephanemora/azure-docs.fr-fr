---
title: Préparer le réseau de votre organisation à Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrir la configuration réseau requise pour l’appel vocal et vidéo Azure Communication Services
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c7ec360d961c0960dc941c48626c3aeb3f7367ec
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108289009"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Garantir un contenu multimédia de qualité supérieure dans Azure Communication Services

Ce document fournit une vue d’ensemble des facteurs et des bonnes pratiques à prendre en considération lors de la création d’expériences de communication multimédia d’excellente qualité avec Azure Communication Services.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Facteurs ayant une incidence sur la qualité et la fiabilité du trafic multimédia

Toutes sortes de facteurs participent à la qualité multimédia (audio, vidéo et de partage d’application) en temps réel d’Azure Communication Services. Parmi eux, la qualité du réseau, la bande passante, le pare-feu, l’hôte et les configurations d’appareil.


### <a name="network-quality"></a>Qualité du réseau

La qualité du trafic multimédia sur IP en temps réel est considérablement impactée par la qualité de la connectivité réseau sous-jacente, mais surtout par l’importance des éléments suivants :
* **Latence**. Temps nécessaire au transport d’un paquet IP d’un point A à un point B sur le réseau. Ce délai de propagation réseau est déterminé par la distance physique entre les deux points et toute charge supplémentaire engendrée par les appareils via lesquels votre trafic circule. La latence est mesurée à sens unique ou selon la durée des boucles (RTT).
* **Perte de paquets**. Pourcentage de paquets perdus dans un laps de temps donné. La perte de paquets affecte directement la qualité du son : elle peut aller de petits paquets individuels perdus n’ayant pratiquement pas d’impact à des pertes de transmission en rafale consécutives qui provoquent une coupure audio complète.
* **Gigue d’arrivée entre paquets ou simple instabilité**. Variation moyenne du délai entre des paquets successifs. Azure Communication Services peut s’adapter à certains niveaux de gigue par le biais de la mise en mémoire tampon. C’est uniquement lorsque la gigue dépasse la mise en mémoire tampon qu’un participant remarque ses effets.

### <a name="network-bandwidth"></a>Bande passante du réseau

Assurez-vous que votre réseau est configuré pour prendre en charge la bande passante nécessaire aux sessions multimédias simultanées d’Azure Communication Services et d’autres applications métier. Tester le chemin réseau de bout en bout à la recherche de goulots d’étranglement sur la bande passante est capital pour la réussite du déploiement de votre solution multimédia Communication Services.

Voici les bandes passantes requises des kits SDK JavaScript :

|Bande passante|Scénarios|
|:--|:--|
|40 Kbits/s|Appel audio d’égal à égal|
|500 Kbits/s|Appel audio et partage d’écran d’égal à égal|
|500 Kbits/s|Appel vidéo de qualité d’égal à égal avec 360p à 30 i/s|
|1,2 Mbits/s|Appel vidéo de qualité HD d’égal à égal avec une résolution de 720p HD à 30 i/s|
|500 Kbits/s|Appel vidéo de groupe de 360p à 30 i/s|
|1,2 Mbits/s|Appel vidéo de groupe HD avec une résolution de 720p HD à 30 i/s| 

Voici les bandes passantes requises pour les kits SDK Android et iOS natifs :

|Bande passante|Scénarios|
|:--|:--|
|30 Kbits/s|Appel audio d’égal à égal |
|130 Kbits/s|Appel audio et partage d’écran d’égal à égal|
|500 Kbits/s|Appel vidéo de qualité d’égal à égal avec 360p à 30 i/s|
|1,2 Mbits/s|Appel vidéo de qualité HD d’égal à égal avec une résolution de 720p HD à 30 i/s|
|1,5 Mbits/s|Appel vidéo de qualité HD d’égal à égal avec une résolution de 1080p HD à 30 i/s |
|500 Kbits/s-1 Mbits/s|Appel vidéo de groupe|
|1 Mbits/s-2 Mbits/s|Appel vidéo de groupe HD (vidéos de 540p sur écran de 1080p)|

### <a name="firewalls-configuration"></a>Configuration de pare-feu

Les connexions Azure Communication Services nécessitent une connectivité Internet à des ports et à des adresses IP spécifiques pour offrir des expériences multimédias d’excellente qualité. Sans accès à ces ports et à ces adresses IP, Azure Communication Services peut malgré tout fonctionner. Toutefois, l’expérience optimale est fournie lorsque les ports et les plages d’adresses IP recommandés sont ouverts.

| Category | Plages d’adresses IP ou nom de domaine complet | Ports | 
| :-- | :-- | :-- |
| Traffic multimédia | [Plage d’adresses IP du cloud public Azure](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 à 3481, ports TCP 443 |
| Signalisation, télémétrie, inscription| *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Optimisation du réseau

Les tâches suivantes sont facultatives et ne sont pas exigées pour le déploiement d’Azure Communication Services. Utilisez ces conseils pour optimiser les performances de votre réseau et celles d’Azure Communication Services, ou encore si vous avez connaissance de l’existence de limitations du réseau.
Vous pouvez souhaiter optimiser davantage dans les situations suivantes :
* Azure Communication Services s’exécute lentement (la bande passante est peut-être insuffisante)
* Les appels sont sans cesse abandonnés (éventuellement dû à des obstacles de pare-feu ou de proxy)
* Les appels souffrent de parasites et de coupures, ou les voix sont métalliques et hachées (gigues ou pertes de paquets possibles)

| Tâche d’optimisation du réseau | Détails |
| :-- | :-- |
| Planifier votre réseau | Les conditions minimales exigées pour les appels sur votre réseau sont développées dans cette documentation. Reportez-vous à l’[Exemple Teams pour la planification de votre réseau](/microsoftteams/tutorial-network-planner-example) |
| Résolution de nom externe | Vérifiez que tous les ordinateurs exécutant les kits SDK Azure Communication Services peuvent résoudre les requêtes DNS externes pour découvrir les services fournis par Azure Communication Services, et que vos pare-feu n’empêchent pas l’accès. Assurez-vous que les kits SDK peuvent résoudre les adresses *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io  |
| Conserver la persistance des sessions | Assurez-vous que votre pare-feu ne change pas les adresses ou les ports de traduction d’adresses réseau (NAT) mappés pour UDP
Valider la taille du pool NAT | Validez la taille du pool de traduction d’adresses réseau (NAT) nécessaire pour la connectivité utilisateur. Lorsque plusieurs utilisateurs et appareils accèdent à Azure Communication Services à l’aide de la [traduction d’adresses réseau (NAT) ou du jeton d’accès personnel (PAT)](/office365/enterprise/nat-support-with-office-365), veillez à ce que les appareils cachés derrière chaque adresse IP routable publiquement ne dépassent pas le nombre pris en charge. Assurez-vous que les adresses IP publiques adéquates sont affectées aux pools NAT pour empêcher l’épuisement des ports. L’épuisement des ports concourra à l’impossibilité des utilisateurs et des appareils internes de se connecter aux services Azure Communication Services |
| Guide de détection et de prévention des intrusions | Si un système de [détection d’intrusion](../../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md) ou de prévention d’intrusion (IDS/IPS) est déployé au sein de votre environnement pour obtenir une couche supplémentaire de sécurité destinée aux connexions sortantes, autorisez toutes les URL Azure Communication Services |
| Configurer un VPN de tunneling fractionné | Nous vous recommandons de fournir un autre chemin pour le trafic Teams qui contourne le réseau privé virtuel (VPN), communément appelé [VPN de tunneling fractionné](/windows/security/identity-protection/vpn/vpn-routing). Le tunneling fractionné signifie que le trafic destiné à Azure Communication Services ne passe pas par le VPN, mais qu’il va directement vers Azure. Le contournement de votre VPN aura un impact positif sur la qualité multimédia et réduira la charge des appareils VPN et du réseau de l’organisation. Pour implémenter un VPN à tunneling fractionné, rapprochez-vous de votre fournisseur de VPN. Voici d’autres raisons pour lesquelles nous conseillons le contournement du VPN : <ul><li> Les VPN ne sont généralement pas conçus ni configurés pour prendre en charge le trafic multimédia en temps réel.</li><li> Les VPN peuvent également ne pas prendre en charge le protocole UDP (nécessaire pour Azure Communication Services)</li><li>Les VPN introduisent également une couche supplémentaire de chiffrement sur le trafic multimédia déjà chiffré.</li><li>La connectivité à Azure Communication Services peut ne pas être optimisée en raison du trafic en épingle à cheveux passant par un appareil VPN.</li></ul>|
| Implémenter QoS | Vous pouvez [utiliser la Qualité de service](/microsoftteams/qos-in-teams) pour configurer la hiérarchisation des paquets. La qualité des appels s’en trouvera améliorée, vous pourrez aussi mieux superviser et résoudre les problèmes de qualité des appels. La Qualité de service doit être implémentée sur tous les segments d’un réseau managé. Même lorsqu’un réseau a été correctement provisionné pour la bande passante, la Qualité de service permet d’atténuer les risques en cas d’événements réseau imprévus. Avec la Qualité de service, le trafic vocal est prioritaire pour que ces événements imprévus n’aient pas d’incidence négative sur la qualité. | 
| Optimiser le Wi-Fi | À l’instar du VPN, les réseaux Wi-Fi ne sont pas nécessairement conçus ou configurés pour prendre en charge le trafic multimédia en temps réel. La planification ou l’optimisation d’un réseau Wi-Fi pour la prise en charge d’Azure Communication Services est un élément important à prendre en considération dans un déploiement de qualité supérieure. Tenez compte de ces facteurs : <ul><li>Implémentez la Qualité de service ou WWM (WiFi Multimedia) pour vérifier que le trafic multimédia est correctement classé par ordre de priorité sur vos réseaux Wi-Fi.</li><li>Planifiez et optimisez les bandes Wi-Fi et l’emplacement des points d’accès. La plage de 2,4 GHz peut fournir une expérience appropriée en fonction de l’emplacement des points d’accès, mais d’autres appareils grand public fonctionnant dans cette plage influent souvent sur les points d’accès. La plage de 5 GHz est mieux adaptée au trafic multimédia en temps réel du fait de sa plage dense, mais elle nécessite plus de points d’accès pour bénéficier d’une couverture suffisante. Les points de terminaison doivent également prendre en charge cette plage et être configurés pour tirer parti de ces bandes en proportion.</li><li>Si vous utilisez des réseaux Wi-Fi double bande, envisagez d’implémenter le pilotage de bande. Le pilotage de bande est une technique mise en œuvre par les fournisseurs Wi-Fi pour inciter les clients bibande à utiliser la plage de 5 GHz.</li><li>Lorsque les points d’accès du même canal sont trop proches les uns des autres, ils peuvent provoquer un chevauchement des signaux et une concurrence involontaire, entraînant une dégradation de l’expérience utilisateur. Vérifiez que les points d’accès situés les uns à côté des autres se trouvent sur des canaux qui ne se chevauchent pas.</li></ul> Chaque fournisseur de service sans fil a ses recommandations propres concernant le déploiement de sa solution sans fil. Pour des instructions spécifiques, renseignez-vous auprès votre fournisseur Wi-Fi.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>Système d’exploitation et navigateurs (pour kits SDK JavaScript)

Les kits SDK vocaux/vidéo Azure Communication Services prennent en charge certains systèmes d’exploitation et navigateurs.
Découvrez les systèmes d’exploitation et les navigateurs gérés par les kits SDK Appel dans la [documentation conceptuelle de l’appel](./calling-sdk-features.md).

## <a name="next-steps"></a>Étapes suivantes

Les documents suivants peuvent vous intéresser :

- En savoir plus sur les [bibliothèques d’appel](./calling-sdk-features.md)
- En savoir plus sur l’[architecture client-serveur](../client-and-server-architecture.md)
- En savoir plus sur les [Topologies de flux d’appels](../call-flows.md)