---
title: Architecture réseau Azure
description: Cet article fournit une description générale du réseau d’infrastructures Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727176"
---
# <a name="azure-network-architecture"></a>Architecture réseau Azure
L’architecture réseau Azure suit une version modifiée du modèle standard cœur/distribution/accès, avec des couches matérielles distinctes. Les couches sont les suivantes :

- Cœur (routeurs de centre de données)
- Distribution (routeurs d’accès et agrégation L2). La couche Distribution sépare le routage L3 de la commutation L2.
- Accès (commutateurs d’hôte L2)

L’architecture réseau a deux niveaux de commutateurs de couche 2. Une couche regroupe le trafic de l’autre couche. La seconde couche effectue des boucles pour incorporer la redondance. Cette architecture des avantages comme une empreinte VLAN plus souple et une meilleure mise à l’échelle des ports. L’architecture garde les couches L2 et L3 bien distinctes, ce qui permet d’utiliser le matériel dans chacune des couches du réseau et de minimiser l’impact d’une défaillance d’une couche sur la ou les autres couches. L’utilisation de troncs permet de partager des ressources, comme la connectivité à l’infrastructure L3.

## <a name="network-configuration"></a>Configuration réseau
L’architecture réseau d’un cluster Azure dans un centre de données se compose des appareils suivants :

- Routeurs (centre de données, routeur d’accès et routeurs leaf en bordure)
- Commutateurs (d’agrégation et TOR [Top of Rack])
- Digi CM
- Unités de distribution de l’alimentation

Azure propose deux architectures distinctes. Certains clients Azure et services partagés existants se trouvent dans l’architecture LAN par défaut (DLA) tandis que les nouvelles régions et les nouveaux clients virtuels sont situés dans l’architecture Quantum 10 (Q10). L’architecture DLA est une conception d’arborescence traditionnelle avec des routeurs d’accès actifs-passifs et des listes de contrôle d’accès (ACL) de sécurité appliquées aux routeurs d’accès. L’architecture Quantum 10 est une conception Close/Mesh de routeurs, où les listes ACL ne sont pas appliquées aux routeurs, mais sous le routage via des réseaux VLAN à équilibrage de charge logicielle (SLB) ou définis par les logiciels.

Le diagramme suivant donne une vue d’ensemble détaillée de l’architecture réseau dans un cluster Azure :

![Diagramme du réseau Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Appareils Quantum 10
La conception de Quantum 10 distribue les commutateurs de couche 3 sur plusieurs appareils dans une conception CLOS/maillée. Les avantages de la conception Q10 présentent une plus grande capacité à mettre à l’échelle une infrastructure réseau existante. La conception utilise des routeurs leaf en bordure, des commutateurs spine et des routeurs Top of Rack pour transmettre le trafic aux clusters sur plusieurs itinéraires permettant la tolérance de panne. Les services de sécurité comme la traduction d’adresses réseau sont gérés via l’équilibrage de charge logicielle, et non sur les appareils matériels.

### <a name="access-routers"></a>Routeurs d’accès
Les routeurs de distribution/d’accès L3 exécutent les principales fonctionnalités de routage pour les couches de distribution et d’accès. Ces périphériques sont déployés sous forme de paires et constituent la passerelle par défaut des sous-réseaux. Chaque paire de routeurs d’accès peut prendre en charge plusieurs paires de commutateurs d’agrégation L2 en fonction de la capacité. Le nombre maximal dépend de la capacité du périphérique, ainsi que des domaines de défaillance. Le nombre est généralement de trois paires de commutateurs d’agrégation L2 par paire de routeurs d’accès.

### <a name="l2-aggregation-switches"></a>Commutateurs d’agrégation L2  
Ces périphériques servent de point d’agrégation pour le trafic L2. Ils constituent la couche de distribution de la structure fabric L2 et peuvent gérer de grandes quantités de trafic. Étant donné que ces appareils agrègent le trafic, la fonctionnalité 802.1q est nécessaire et les technologies de bande passante élevée telles que l’agrégation des ports et 10GE sont utilisées.

### <a name="l2-host-switches"></a>Commutateurs d’hôte L2
Les hôtes se connectent directement à ces commutateurs. Ils peuvent être montés en rack ou déployés en châssis. La norme 802.1q permet de désigner un réseau VLAN comme réseau VLAN natif, tout en traitant celui-ci comme une structure Ethernet normale (non marquée). Dans des circonstances normales, les trames du réseau VLAN natif sont transmises et reçues non marquées sur un port de tronc 802.1q. Cette fonctionnalité a été conçue pour la migration vers 802.1q et la compatibilité avec des périphériques non-802.1q. Dans cette architecture, seule l’infrastructure réseau utilise le réseau VLAN natif.

Cette architecture spécifie une norme pour la sélection de réseau VLAN natif. La norme garantit, si possible, que les appareils de routeur d’accès ont un réseau VLAN natif unique pour chaque tronc et les troncs L2Aggregation à L2Aggregation. Les troncs de commutateur L2Aggregation à L2Host ont un réseau VLAN natif non défini par défaut.

### <a name="link-aggregation-8023ad"></a>Agrégation de liens (802.3ad)
L’agrégation de liens permet à plusieurs liens individuels d’être regroupés et traités comme un seul lien logique. Le numéro utilisé pour désigner les interfaces port-canal doit être standardisé pour faciliter le débogage des opérations. Le reste du réseau utilise le même numéro aux deux extrémités d’un port-canal.

Les numéros spécifiés pour le commutateur L2Agg à L2Host sont les numéros de port-canal utilisés sur le côté L2Agg. Étant donné que la plage de numéros est plus limitée sur le côté L2Host, la norme consiste à utiliser les numéros 1 et 2 sur le côté L2Host. Ceux-ci font référence au port-canal qui va côté « a » et « b » respectivement.

### <a name="vlans"></a>Réseaux VLAN
L’architecture réseau utilise des réseaux VLAN pour regrouper les serveurs dans un seul domaine de diffusion. Les numéros de réseau VLAN sont conformes à la norme 802.1q, qui prennent en charge les réseaux VLAN numérotés de 1 à 4094.

### <a name="customer-vlans"></a>Réseaux VLAN de clients
Vous avez plusieurs options d’implémentation de réseau VLAN que vous pouvez déployer par le biais du portail Azure pour satisfaire les besoins de séparation et d’architecture de votre solution. Vous déployez ces solutions via des machines virtuelles. Pour obtenir des exemples d’architecture de référence de client, consultez [Architectures de référence Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architecture de périphérie
Les centres de données Azure reposent sur des infrastructures réseau hautement redondantes et bien provisionnées. Microsoft implémente les réseaux des centres de données Azure avec des architectures à redondance « need plus one » (N+1) ou supérieures. Des fonctionnalités de basculement complètes dans et entre les centres de données permettent de garantir la disponibilité du réseau et des services. En externe, les centres de données sont desservis par des circuits réseau haut débit dédiés. Ces circuits connectent des propriétés de manière redondante, avec plus de 1 200 fournisseurs de services Internet dans le monde entier à de multiples points de peering. Cela offre plus de 2 000 Gbits/s de capacité de périphérie potentielle sur le réseau.

Les routeurs de filtrage à la couche de périphérie et d’accès du réseau Azure offrent une sécurité bien établie au niveau des paquets et permettent d'empêcher les tentatives non autorisées de connexion à Azure. Les routeurs permettent de vous assurer que le contenu réel des paquets présente des données au format attendu et qu’il est conforme au schéma de communication client/serveur attendu. Azure implémente une architecture hiérarchisée qui est constituée des composants de séparation de réseau et de contrôle d’accès suivants :

- **Routeurs de périphérie.** Ils séparent l’environnement des applications d’Internet. Les routeurs de périphérie sont conçus pour fournir une protection anti-usurpation et limiter l’accès à l’aide de listes ACL.
- **Routeurs de distribution (accès).** Ils autorisent uniquement les adresses IP approuvées par Microsoft, fournissent une protection anti-usurpation et établissent des connexions à l’aide de listes ACL.

### <a name="ddos-mitigation"></a>Atténuation des risques liés à DDoS
Les attaques par déni de service distribué (DDoS) continuent de représenter une véritable menace pour la fiabilité des services en ligne. Comme les attaques deviennent plus ciblées et plus sophistiquées et que les services de Microsoft sont plus dispersés d’un point de vue géographique, l’identification et la réduction de l’impact de ces attaques est une priorité de premier ordre.

Le service [Azure DDos Protection standard](../../virtual-network/ddos-protection-overview.md) assure une excellente défense contre les attaques DDoS. Pour en savoir plus, consultez [Protection DDoS Azure : Bonnes pratiques et architectures de référence](ddos-best-practices.md).

> [!NOTE]
> Microsoft fournit une protection DDoS par défaut à tous les clients Azure.
>
>

## <a name="network-connection-rules"></a>Règles de connexion au réseau
Azure déploie sur son réseau des routeurs de périphérie qui offrent une sécurité au niveau des paquets pour empêcher les tentatives non autorisées de connexion à Azure. Les routeurs de périphérie assurent que le contenu réel des paquets présente des données au format attendu et qu’il est conforme au schéma de communication client/serveur attendu.

Les routeurs de périphérie séparent l’environnement des applications d’Internet. Ces routeurs sont conçus pour fournir une protection anti-usurpation et limiter l’accès à l’aide de listes ACL. Microsoft configure les routeurs de périphérie à l’aide d’une approche ACL hiérarchisée pour limiter les protocoles réseau qui sont autorisés à transiter les routeurs de périphérie et les routeurs d’accès.

Microsoft positionne les appareils réseau aux emplacements d’accès et de périphérie pour servir de points de limite où s’appliquent les filtres d’entrée ou de sortie.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](physical-security.md)
- [Disponibilité de l’infrastructure Azure](infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](infrastructure-components.md)
- [Réseau de production Azure](production-network.md)
- [Fonctionnalités de sécurité d’Azure SQL Database](infrastructure-sql.md)
- [Opérations de production et administration Azure](infrastructure-operations.md)
- [Surveillance de l’infrastructure Azure](infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](infrastructure-integrity.md)
- [Protection des données client Azure](protection-customer-data.md)


