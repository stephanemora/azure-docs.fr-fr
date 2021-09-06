---
title: Fichier Include
description: inclure fichier
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a237122aaeba57d8c3aa99274a0cc41232729eca
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676930"
---
### <a name="is-nat-supported-on-all-azure-vpn-gateway-skus"></a>Le protocole NAT est-il pris en charge sur toutes les références SKU de passerelle VPN Azure ?

NAT est pris en charge sur VpnGw2~5 et VpnGw2AZ~5AZ.

### <a name="can-i-use-nat-on-vnet-to-vnet-or-p2s-connections"></a>Puis-je utiliser NAT sur des connexions de réseau virtuel à réseau virtuel ou P2S ?

Non, NAT est pris en charge uniquement sur les connexions **IPsec** intersites.

### <a name="how-many-nat-rules-can-i-use-on-a-vpn-gateway"></a>Combien de règles NAT puis-je utiliser sur une passerelle VPN ?

Vous pouvez créer jusqu’à 100 règles NAT (règles d’entrée et de sortie combinées) sur une passerelle VPN.

### <a name="is-nat-applied-to-all-connections-on-a-vpn-gateway"></a>NAT s’applique-t-il à toutes les connexions sur une passerelle VPN ?

NAT s’applique aux connexions avec des règles NAT. Si une connexion n’a pas de règle NAT, NAT n’est pas effectif sur cette connexion. Sur la même passerelle VPN, des connexions avec NAT et d’autres connexions sans NAT peuvent fonctionner ensemble.

### <a name="what-types-of-nat-is-supported-on-azure-vpn-gateways"></a>Quels types de NAT sont pris en charge sur les passerelles VPN Azure ?

NAT 1:1 statique uniquement. NAT dynamique ou NAT64 n’est PAS pris en charge.

### <a name="does-nat-work-on-active-active-vpn-gateways"></a>NAT fonctionne-t-il sur des passerelles VPN de type actif/actif ?

Oui. NAT fonctionne sur les passerelles VPN de type actif/actif et actif/passif.

### <a name="does-nat-work-with-bgp-connections"></a>NAT fonctionne-t-il avec les connexions BGP ?

Oui, vous pouvez utiliser BGP avec NAT. Voici quelques points importants à prendre en compte :

* Sélectionnez **Activer la traduction d’itinéraire BGP** sur la page de configuration des règles NAT pour vous assurer que les itinéraires appris et les itinéraires publiés sont traduits en préfixes d’adresse post-NAT (mappages externes) en fonction des règles NAT associées aux connexions. Vous devez vous assurer que les routeurs BGP locaux publient les préfixes exacts tels qu’ils sont définis dans les règles IngressSNAT.

* Si le routeur VPN local utilise APIPA (169.254.x.x) comme adresse IP du locuteur/homologue BGP, utilisez l’adresse APIPA directement dans le champ **Adresse IP de l’homologue BGP** de la passerelle de réseau local. Si le routeur VPN local utilise une adresse non APIPA standard et qu’elle est en conflit avec l’espace d’adressage du réseau virtuel ou d’autres espaces réseaux locaux, vérifiez que la règle IngressSNAT traduit l’adresse IP de l’homologue BGP en une adresse unique et non superposée, puis insérez l’adresse post-NAT dans le champ **Adresse IP de l’homologue BGP** de la passerelle de réseau local.

### <a name="do-i-need-to-create-the-matching-dnat-rules-for-the-snat-rule"></a>Dois-je créer les règles DNAT correspondantes pour la règle SNAT ?

Non. Une règle SNAT unique définit la traduction dans **les deux** directions d’un réseau particulier :

* Une règle IngressSNAT définit la traduction des adresses IP source entrantes **dans** la passerelle VPN Azure à partir du réseau local. Elle gère également la traduction des adresses IP de destination qui quittent le réseau virtuel vers le même réseau local.

* Une règle EgressSNAT définit la traduction des adresses IP source de réseau virtuel sortantes de la passerelle VPN Azure vers les réseaux locaux. Elle gère également la traduction des adresses IP de destination pour les paquets arrivant dans le réseau virtuel via ces connexions à l’aide de la règle EgressSNAT.

* Dans les deux cas, aucune règle **DNAT** n’est nécessaire.

### <a name="what-do-i-do-if-my-vnet-or-local-network-gateway-address-space-has-two-or-more-prefixes-can-i-apply-nat-to-all-of-them-or-just-a-subset"></a>Que dois-je faire si mon réseau virtuel ou l’espace d’adressage de la passerelle de réseau local comporte au moins deux préfixes ? Puis-je Appliquer NAT à tous ? Ou à un sous-ensemble seulement ?

Vous devez créer une règle NAT pour chaque préfixe dont vous avez besoin pour la traduction d’adresses réseau, car chaque règle NAT ne peut inclure qu’un seul préfixe d’adresse pour NAT. Par exemple, si l’espace d’adressage de la passerelle de réseau local est 10.0.1.0/24 et 10.0.2.0/25, vous pouvez créer deux règles comme indiqué ci-dessous :

* IngressSNAT règle 1: mapper 10.0.1.0/24 à 100.0.1.0/24
* IngressSNAT règle 2 : mapper 10.0.2.0/25 à 100.0.2.0/25

Les deux règles doivent correspondre aux longueurs des préfixes d’adresse correspondants. Il en va de même pour les règles EgressSNAT pour l’espace d’adressage du réseau virtuel.

> [!IMPORTANT]
> Si vous ne liez qu’une seule règle à la connexion ci-dessus, l’autre espace d'adressage ne sera **PAS** traduit.

### <a name="can-i-use-different-egresssnat-rules-to-translate-my-vnet-address-space-to-different-prefixes-to-different-on-premises-networks"></a>Puis-je utiliser différentes règles EgressSNAT pour traduire mon espace d’adressage de réseau virtuel en différents préfixes sur différents réseaux locaux ?

Oui, vous pouvez créer plusieurs règles EgressSNAT pour le même espace d’adressage de réseau virtuel et appliquer les règles EgressSNAT à différentes connexions. Pour les connexions sans règle EgressSNAT,

### <a name="can-i-use-the-same-ingresssnat-rule-on-different-connections"></a>Puis-je utiliser la même règle IngressSNAT sur différentes connexions ?

Oui, c’est généralement le cas lorsque les connexions sont destinées au même réseau local pour assurer la redondance. Vous ne pouvez pas utiliser la même règle d’entrée si les connexions concernent des réseaux locaux différents.

### <a name="do-i-need-both-ingress-and-egress-rules-on-a-nat-connection"></a>Ai-je besoin de règles d’entrée et de sortie sur une connexion NAT ?

Vous avez besoin de règles d’entrée et de sortie sur la même connexion lorsque l’espace d’adressage du réseau local chevauche l’espace d’adressage du réseau virtuel. Si l’espace d’adressage du réseau virtuel est unique sur tous les réseaux connectés, vous n’avez pas besoin de la règle EgressSNAT sur ces connexions. Vous pouvez utiliser les règles d’entrée pour éviter un chevauchement d’adresses sur les réseaux locaux.