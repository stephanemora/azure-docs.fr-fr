---
title: Architecture réseau Azure
description: Cet article fournit une description générale du réseau d’infrastructures Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101431"
---
# <a name="azure-network-architecture"></a>Architecture réseau Azure
L’architecture réseau Azure suit une version modifiée du modèle standard Cœur/Distribution/Accès, avec des couches matérielles distinctes. Les couches sont les suivantes :

- Cœur (routeurs de centre de données)
- Distribution (routeurs d’accès et agrégation L2), la couche Distribution sépare le routage L3 de la commutation L2
- Accès (commutateurs d’hôte L2)

L’architecture réseau a deux niveaux de commutateurs de couche 2, une couche agrégeant le trafic de l’autre couche, et des boucles de couche 2 pour incorporer la redondance. Elle offre des avantages comme une empreinte VLAN plus souple et une meilleure mise à l’échelle des ports. L’architecture garde les couches L2 et L3 bien distinctes, ce qui permet d’utiliser le matériel dans chacune des couches du réseau et de minimiser l’impact d’une défaillance d’une couche sur la ou les autres couches. L’utilisation de troncs permet de partager des ressources, comme la connectivité à l’infrastructure L3.

## <a name="network-configuration"></a>Configuration réseau
L’architecture réseau d’un cluster Microsoft Azure dans un centre de données se compose des périphériques suivants :

- Routeurs (centre de données, routeur d’accès et routeurs leaf en bordure)
- Commutateurs (d’agrégation et TOR [Top of Rack])
- Digi CM
- PDU ou Nucleons

La figure ci-dessous offre une vue d’ensemble de l’architecture réseau Azure au sein d’un cluster. Azure propose deux architectures distinctes. Certains clients Azure et services partagés existants se trouvent dans l’architecture LAN par défaut (DLA) tandis que les nouvelles régions et les nouveaux clients virtuels sont accessibles via l’architecture Quantum 10 (Q10). L’architecture DLA est une conception d’arborescence traditionnelle avec des routeurs d’accès actifs-passifs et des listes ACL de sécurité appliquées aux routeurs d’accès. L’architecture Quantum 10 est une conception Clos/maillée de routeurs où les listes ACL ne sont pas appliquées sur les routeurs, mais sous le routage des réseaux VLAN à équilibrage de charge logicielle (SLB) ou définis par les logiciels.

![Réseau Azure][1]

### <a name="quantum-10-devices"></a>Périphériques Quantum 10
La conception de Quantum 10 distribue les commutateurs de couche 3 sur plusieurs périphériques dans une conception CLOS/maillée. Les avantages de la conception Q10 présentent une plus grande capacité à mettre à l’échelle une infrastructure réseau existante. La conception utilise des routeurs leaf en bordure, des commutateurs spine et des routeurs Top of Rack pour transmettre le trafic aux clusters sur plusieurs routes permettant la tolérance de panne. Les services de sécurité comme la traduction d’adresses réseau sont gérés via l’équilibrage de charge logicielle, et non sur les périphériques matériels.

### <a name="access-routers"></a>Routeurs d’accès
Les routeurs de distribution/d’accès L3 exécutent les principales fonctionnalités de routage pour les couches de distribution et d’accès. Ces périphériques sont déployés sous forme de paires et constituent la passerelle par défaut des sous-réseaux. Chaque paire de routeurs d’accès peut prendre en charge plusieurs paires de commutateurs d’agrégation L2 en fonction de la capacité. Le nombre maximal dépend de la capacité du périphérique, ainsi que des domaines de défaillance. En fonction de la capacité attendue, le nombre serait généralement de trois paires de commutateurs d’agrégation L2 par paire de commutateurs d’accès.

### <a name="l2-aggregation-switches"></a>Commutateurs d’agrégation L2  
Ces périphériques servent de point d’agrégation pour le trafic L2. Ils constituent la couche de distribution de la structure fabric L2 et peuvent gérer de grandes quantités de trafic. Étant donné que ces périphériques agrègent le trafic, la fonctionnalité 802.1q est nécessaire et les technologies de bande passante élevée telles que l’agrégation des ports et 10GE sont utilisées.

### <a name="l2-host-switches"></a>Commutateurs d’hôte L2
Les hôtes se connectent directement à ces commutateurs. Ils peuvent être montés en rack ou déployés en châssis. La norme 802.1q permet de désigner un réseau VLAN comme réseau VLAN natif, tout en traitant celui-ci comme une structure Ethernet normale (non marquée). Dans des circonstances normales, les trames du réseau VLAN natif sont transmises et reçues non marquées sur un port de tronc 802.1q. Cette fonctionnalité a été conçue pour la migration vers 802.1q et la compatibilité avec des périphériques non-802.1q. Dans cette architecture, seule l’infrastructure réseau utilise le réseau VLAN natif.

Cette architecture spécifie une norme pour la sélection de réseau VLAN natif qui garantit, si possible, que les périphériques de routeur d’accès ont un réseau VLAN natif unique pour chaque tronc et les troncs L2Aggregation à L2Aggregation. Les troncs de commutateur L2Aggregation à L2Host ont un réseau VLAN natif non défini par défaut.

### <a name="link-aggregation-8023ad"></a>Agrégation de liens (802.3ad)
L’agrégation de liens (LAG) permet à plusieurs liens individuels d’être regroupés et traités comme un seul lien logique. Le numéro utilisé pour désigner les interfaces port-canal doit être standardisé pour faciliter le débogage des opérations, le reste du réseau utilisera le même numéro aux deux extrémités d’un port-canal. Cela nécessite un standard pour déterminer l’extrémité du port-canal à utiliser pour définir le prochain numéro disponible.

Les numéros spécifiés pour le commutateur L2Agg à L2Host sont les numéros de port-canal utilisés sur le côté L2Agg. Étant donné que la plage de numéros est plus limitée sur le côté L2Host, le standard consiste à utiliser les numéros 1 et 2 sur le côté L2Host pour faire référence au port-canal qui va côté « a » et « b » respectivement.

### <a name="vlans"></a>Réseaux VLAN
L’architecture réseau utilise des réseaux VLAN pour regrouper les serveurs dans un seul domaine de diffusion. Les numéros de réseau VLAN sont conformes aux standards de 802.1q, lesquels prennent en charge les réseaux VLAN numérotés de 1 à 4 094.

### <a name="customer-vlans"></a>Réseaux VLAN de clients
Les clients ont plusieurs options d’implémentation de réseau VLAN qu’ils peuvent déployer par le biais du portail Azure pour satisfaire les besoins de séparation et d’architecture de leur solution. Ces solutions sont déployées via des machines virtuelles. Pour des exemples d’architecture de référence de client, visitez [Architectures de référence Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architecture de périphérie
Les centres de données Azure reposent sur des infrastructures réseau hautement redondantes et bien provisionnées. Les réseaux des centres de données Azure sont implémentés avec des architectures à redondance « need plus one » (N + 1) ou mieux. Des fonctionnalités de basculement complètes dans et entre les centres de données permettent de garantir la disponibilité du réseau et des services. En externe, les centres de données sont desservis par des circuits réseau haut débit dédiés qui connectent des propriétés de manière redondante, avec plus de 1 200 fournisseurs de services Internet dans le monde entier à de multiples points d’appairage, offrant plus de 2 000 Gbits/s de capacité de périphérie potentielle sur le réseau.

Les routeurs de filtrage à la couche de périphérie et d’accès du réseau Microsoft Azure offrent une sécurité bien établie au niveau des paquets pour empêcher les tentatives non autorisées de connexion à Azure. Ils permettent de vous assurer que le contenu réel des paquets présente des données au format attendu et qu’il est conforme au schéma de communication client/serveur attendu. Azure implémente une architecture hiérarchisée qui est constituée des composants de séparation de réseau et de contrôle d’accès suivants :

- Routeurs de périphérie : Séparent l’environnement d’applications d’Internet. Les routeurs de périphérie sont conçus pour fournir une protection anti-usurpation et limiter l’accès à l’aide de listes de contrôle d’accès (ACL).
- Routeurs de distribution (accès) : Les routeurs d’accès sont conçus pour autoriser uniquement les adresses IP approuvées par Microsoft, fournir une protection anti-usurpation et établir des connexions à l’aide de listes ACL.

### <a name="a10-ddos-mitigation-architecture"></a>Architecture d’atténuation DDOS A10
Les attaques par déni de service continuent de représenter une véritable menace pour la fiabilité des services en ligne de Microsoft. Comme les attaques deviennent plus ciblées et plus sophistiquées et que les services de Microsoft sont plus dispersés d’un point de vue géographique, la capacité à fournir des mécanismes efficaces pour identifier et réduire l’impact de ces attaques est une priorité de premier ordre.

Les informations suivantes vous expliquent comment le système d’atténuation DDOS A10 est implémenté dans une perspective d’architecture réseau.  
Microsoft Azure utilise des périphériques réseau A10 au niveau du routeur de centre de données, qui offrent une atténuation et une détection automatisées. La solution A10 utilise Azure Network Monitoring pour échantillonner des paquets de flux Net et déterminer si une attaque a lieu. Une fois que l’attaque est détectée, les périphériques A10 sont utilisés comme nettoyeurs pour atténuer les attaques. Après l’atténuation, un trafic plus propre est autorisé dans le centre de données Azure directement depuis le routeur de centre de données. La solution A10 est utilisée pour protéger l’infrastructure réseau Azure.

Les protections DDoS de la solution A10 sont les suivantes :

- Protection contre la saturation de flux UDP IPv4 et IPv6
- Protection contre la saturation de flux ICMP IPv4 et IPv6
- Protection contre la saturation de flux TCP IPv4 et IPv6
- Protection contre les attaques TCP SYN pour IPv4 et IPv6
- Attaque par fragmentation

> [!NOTE]
> La protection DDoS est fournie par défaut à tous les clients Azure.
>
>

## <a name="network-connection-rules"></a>Règles de connexion au réseau
Azure déploie sur son réseau des routeurs de périphérie qui offrent une sécurité au niveau des paquets pour empêcher les tentatives non autorisées de connexion à Microsoft Azure. Ils assurent que le contenu réel des paquets présente des données au format attendu et qu’il est conforme au schéma de communication client/serveur attendu.

Les routeurs de périphérie séparent l’environnement des applications d’Internet. Les routeurs de périphérie sont conçus pour fournir une protection anti-usurpation et limiter l’accès à l’aide de listes de contrôle d’accès (ACL). Ils sont configurés à l’aide d’une approche ACL hiérarchisée pour limiter les protocoles réseau qui sont autorisés à transiter les routeurs de périphérie et les routeurs d’accès.

Les périphériques réseau sont positionnés aux emplacements d’accès et de périphérie et servent de points de limite où s’appliquent les filtres d’entrée et/ou de sortie.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](azure-physical-security.md)
- [Disponibilité de l’infrastructure Azure](azure-infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](azure-infrastructure-components.md)
- [Réseau de production Azure](azure-production-network.md)
- [Fonctionnalités de sécurité de Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Opérations de production et administration Azure](azure-infrastructure-operations.md)
- [Monitoring de l’infrastructure Azure](azure-infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](azure-infrastructure-integrity.md)
- [Protection des données des clients dans Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
