---
title: Réseau de production Azure
description: Cet article fournit une description générale du fonctionnement du réseau de production Microsoft Azure.
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
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101550"
---
# <a name="azure-production-network"></a>Réseau de production Azure
Les utilisateurs du réseau de production Azure incluent les clients externes accédant à leurs propres applications Microsoft Azure, ainsi que le support technique interne Microsoft Azure qui gère le réseau de production. Les méthodes d’accès de sécurité et les mécanismes de protection pour établir des connexions au réseau de production Azure sont décrits dans cet article.

## <a name="internet-routing-and-fault-tolerance"></a>Routage Internet et tolérance de panne
Une infrastructure Domain Name Service Microsoft Azure (WADNS) interne et externe globalement redondante, ainsi que plusieurs clusters de serveur Domain Name Service (DNS) principaux et secondaires assurent la tolérance de panne tandis que les contrôles supplémentaires de Microsoft Azure Network Security tels que NetScaler servent à empêcher les attaques par déni de service distribué (DDoS) et à protéger l’intégrité des services Microsoft Azure DNS.

Les serveurs WADNS sont situés dans plusieurs installations de centres de données. L’implémentation de WADNS intègre une hiérarchie de serveurs DNS secondaires/primaires pour résoudre publiquement les noms de domaine client Azure. Les noms de domaine se résolvent généralement en une adresse CloudApp.net, qui encapsule l’adresse IP virtuelle (VIP) pour le service du client. Unique à Azure, l’adresse IP virtuelle correspondant à l’adresse IP dédiée (DIP) interne de la traduction du locataire est effectuée par les équilibreurs de charge Microsoft responsables de cette adresse IP virtuelle.

Azure est hébergé dans des centres de données Azure géographiquement répartis dans tous les États-Unis et repose sur des plateformes de routage de pointe qui implémentent des normes architecturales robustes et évolutives. Voici certaines des fonctionnalités notables :

- Ingénierie de trafic basée sur le MPLS fournissant une utilisation efficace des liens et une dégradation sans pertes du service en cas de panne
- Les réseaux sont implémentés avec des architectures à redondance « need plus one » (N + 1) ou mieux.
- En externe, les centres de données sont desservis par des circuits réseau dédiés haut débit qui connectent des propriétés de manière redondante, avec plus de 1 200 fournisseurs de services Internet dans le monde entier à de multiples points de peering en fournissant plus de 2 000 Go par seconde (Gbits/s) de capacité de périphérie.

Comme Microsoft possède ses propres circuits réseau entre centres de données, ces attributs l’offre Azure à atteindre une disponibilité du réseau supérieure à 99,9 % sans faire appel aux fournisseurs de services Internet tiers traditionnels.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Connexion au réseau de production et pare-feux associés
La stratégie de flux de trafic Internet du réseau Azure dirige le trafic vers le réseau de production Azure situé dans le centre de données régional le plus proche au sein des États-Unis. Dans la mesure où les centres de données de production Azure maintiennent une architecture et un matériel réseau cohérents, la description du flux de trafic ci-dessous s’applique systématiquement à tous les centres de données.

Une fois que le trafic Internet pour Azure est acheminé vers le centre de données le plus proche, une connexion est établie pour les routeurs d’accès. Ces routeurs d’accès servent à isoler le trafic entre les nœuds Azure et les machines virtuelles instanciées par le client. Les périphériques d’infrastructure réseau aux emplacements d’accès et de périphérie sont les points limites où s’appliquent les filtres d’entrée et/ou de sortie. Ces routeurs sont configurés par une liste de contrôle d’accès à plusieurs niveaux afin de filtrer le trafic indésirable et appliquer des limites de transfert de données, si nécessaire. Le trafic autorisé par la liste de contrôle d’accès est acheminé vers les équilibreurs de charge. Les routeurs de distribution sont conçus pour autoriser uniquement les adresses IP approuvées par Microsoft, détecter les cas d’usurpation d’identité et établir les connexions TCP à l’aide de listes ACL.

Les appareils d’équilibrage de charge externe sont situés derrière les routeurs d’accès pour effectuer la traduction d’adresses réseau (NAT) des adresses IP routables par Internet vers les adresses IP internes Azure. Ils renvoient également des paquets vers les ports et les adresses IP internes de production valides, et servent de mécanisme de protection pour limiter l’exposition de l’espace d’adressage du réseau de production interne.

Par défaut, Microsoft met en œuvre le protocole sécurisé HTTPS (Hypertext Transfer Protocol Secure) pour tout le trafic transmis aux navigateurs web du client, y compris la connexion et tout le trafic qui s’ensuit. L’utilisation de TLS v1.2 crée un tunnel sécurisé pour la circulation du trafic. Les ACL sur les routeurs d’accès et de cœur garantissent que la source du trafic est cohérente avec ce qui est attendu.

Une différence importante dans cette architecture par rapport à l’architecture de sécurité traditionnelle est qu’il n’y a aucun dispositif dédié de pare-feu matériel, d’appareils de détection et de prévention d’intrusion spécialisés, ou d’autres appliances de sécurité avant d’établir des connexions à l’environnement de production Azure. Les clients s’attendent en général à trouver ces périphériques de pare-feu matériel dans le réseau Azure. Ces fonctionnalités de sécurité sont intégrées presque exclusivement dans la couche logicielle exécutant l’environnement Azure afin de fournir des mécanismes de sécurité multicouche robustes, y compris les fonctionnalités de pare-feu. En outre, l’étendue de la limite et la prolifération associée d’appareils de sécurité critiques est plus facile à gérer et à inventorier comme illustré ci-dessus, étant donné que tout est géré par le logiciel exécutant Azure.

## <a name="core-security-and-firewall-features"></a>Fonctionnalités de sécurité critique et de pare-feu
Azure implémente des fonctionnalités robustes de sécurité logicielle et de pare-feu à différents niveaux afin d’appliquer les fonctionnalités de sécurité attendues d’un environnement traditionnel pour protéger la limite d’autorisation de sécurité de base.

### <a name="azure-security-features"></a>Fonctionnalités de sécurité Azure
Azure implémente des pare-feu logiciels basés sur l’hôte à l’intérieur du réseau de production. Plusieurs fonctionnalités de sécurité principale et de pare-feu résident dans l’environnement principal Azure. Ces fonctionnalités de sécurité reflètent une stratégie de défense en profondeur dans l’environnement Azure. Les données du client dans Microsoft Azure sont protégées par les pare-feu suivants :

**Pare-feu hyperviseur (filtre de paquets)**  : il est implémenté dans l’hyperviseur et configuré par un agent contrôleur de structure. Ce pare-feu protège le locataire en cours d’exécution à l’intérieur de la machine virtuelle de tout accès non autorisé. Par défaut, lorsqu’une machine virtuelle est créée, tout le trafic est bloqué. Ensuite, l’agent contrôleur de structure ajoute au filtre des règles et exceptions afin de permettre le trafic autorisé.

Il existe deux catégories de règles qui sont programmées ici :

- Règles de configuration de la machine ou d’infrastructure : par défaut, toutes les communications sont bloquées. Certaines exceptions permettent à une machine virtuelle d’envoyer et de recevoir des communications DHCP, des informations DNS, et d’envoyer le trafic vers l’Internet « public », en sortie vers les autres machines virtuelles dans le cluster du contrôleur de structure et le serveur d’activation du système d’exploitation. Étant donné que la liste autorisée de destinations sortantes des machines virtuelles n’inclut pas les sous-réseaux de routeur Microsoft Azure et d’autres propriétés de Microsoft, les règles font office de couche défensive pour ceux-ci.
- Fichier de configuration de rôle : ceci définit les listes de contrôle d’accès entrant en fonction du modèle de service des locataires. Par exemple, si un locataire dispose d’un serveur web frontal sur le port 80 d’une certaine machine virtuelle, le port 80 est alors ouvert à toutes les adresses IP. Si la machine virtuelle a un rôle de travail en cours d’exécution, il est ouvert uniquement sur la machine virtuelle au sein du même locataire.

**Pare-feu d’hôte natif** : la structure et le stockage Microsoft Azure s’exécutent sur un système d’exploitation natif dépourvu d’hyperviseur et, par conséquent, le pare-feu Windows est configuré avec les deux ensembles de règles ci-dessus.

**Pare-feu hôte** : le pare-feu hôte sert à protéger la partition hôte qui exécute l’hyperviseur. Les règles sont programmées pour autoriser uniquement le contrôleur de structure et les « jump boxes » à communiquer avec la partition hôte sur un port spécifique. Les autres exceptions permettent d’autoriser la réponse DHCP et les réponses DNS. Azure utilise un fichier de configuration des machines qui contient le modèle de règles de pare-feu pour la partition hôte. Il existe également une exception de pare-feu hôte qui permet aux machines virtuelles de communiquer avec les composants, le serveur de réseau et le serveur de métadonnées de l’hôte, via un protocole et des ports spécifiques.

**Pare-feu invité** : élément de pare-feu Windows du système d’exploitation invité, configurable par le client sur les machines virtuelles et le stockage du client.

Autres fonctions de sécurité intégrées aux fonctionnalités Azure :

- Les composants d’infrastructure sont des adresses IP affectées qui proviennent d’adresses IP dédiées (DIP). Un attaquant sur Internet ne peut pas envoyer le trafic vers ces adresses, car elles n’atteindraient pas Microsoft. Les routeurs de la passerelle Internet filtrent les paquets destinés uniquement à des adresses internes, afin qu’elles n’entrent pas dans le réseau de production. Les seuls composants qui acceptent le trafic dirigé vers les adresses IP virtuelles sont les équilibreurs de charge.
- Les pare-feu implémentés sur tous les nœuds internes offrent trois principales considérations d’architecture de sécurité pour n’importe quel scénario donné :

   - Ils sont placés derrière l’équilibreur de charge (LB) et acceptent les paquets quelle que soit leur provenance. Ils sont destinées à être exposés en externe et correspondent aux ports ouverts du pare-feu périmétrique traditionnel.
   - Ils acceptent uniquement les paquets à partir d’un ensemble limité d’adresses. Cela fait partie de la stratégie de défense approfondie contre les attaques par déni de service. Ces connexions sont authentifiées par chiffrement.
   - Les pare-feux sont uniquement accessibles à partir de nœuds internes spécifiques, auquel cas ils acceptent les paquets provenant uniquement d’une liste énumérée d’adresses d’IP source, qui sont toutes des adresses IP dynamiques au sein du réseau Azure. Par exemple, une attaque sur le réseau d’entreprise peut diriger les requêtes vers ces adresses, mais celles-ci seraient bloquées sauf si l’adresse source du paquet appartenait à la liste énumérée au sein du réseau Azure.
   - Le routeur d’accès au niveau des paquets sortants des blocs de périmètre sont adressés à une adresse au sein du réseau Azure en raison de ses itinéraires statiques configurés.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](azure-physical-security.md)
- [Disponibilité de l’infrastructure Azure](azure-infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](azure-infrastructure-components.md)
- [Architecture réseau Azure](azure-infrastructure-network.md)
- [Fonctionnalités de sécurité de Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Opérations de production et administration Azure](azure-infrastructure-operations.md)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoring de l’infrastructure Azure)
- [Intégrité de l’infrastructure Azure](azure-infrastructure-integrity.md)
- [Protection of customer data in Azure](azure-protection-of-customer-data.md) (Protection des données client dans Azure)
