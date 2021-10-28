---
title: Que sont les services IP de réseau virtuel Azure ?
description: Vue d’ensemble des services IP du réseau virtuel Microsoft Azure. Découvrez comment fonctionnent les services IP et comment utiliser des ressources IP dans Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subService: ip-services
ms.topic: overview
ms.date: 10/01/2021
ms.custom: template-overview
ms.openlocfilehash: 7b9c1eb8e1e0a067da2f0ed29762ef14ce365690
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257687"
---
# <a name="what-is-azure-virtual-network-ip-services"></a>Que sont les services IP de réseau virtuel Azure ?

Les services IP sont un ensemble de services liés à l’adresse IP qui autorisent la communication dans un réseau virtuel Microsoft Azure. Les adresses IP publiques et privées sont utilisées dans Azure pour la communication entre les ressources. La communication avec les ressources peut se produire dans un réseau virtuel Azure privé et sur l’Internet public.

Les services IP sont constitués des éléments suivants :

* Adresses IP publiques

* Préfixes d’adresse IP publique

* Adresses IP privées

* Préférence de routage

* Préférence de routage non mesurée

## <a name="public-ip-addresses"></a>Adresses IP publiques

Les adresses IP publiques sont utilisées par les ressources Internet pour communiquer avec les ressources (communications entrantes) dans Azure. Les adresses IP publiques peuvent être créées avec une adresse IPv4 et IPv6. Vous avez la possibilité de créer un déploiement à double pile avec une adresse IPv4 et IPv6. Les adresses IP publiques sont disponibles dans deux SKU, **Standard** et **De base**. Les adresses IP publiques peuvent être statiques ou affectées dynamiquement.

Une adresse IP publique est une ressource disposant de ses propres propriétés. Voici quelques-unes des ressources auxquelles vous pouvez associer une adresse IP publique :

* Interfaces réseau de machine virtuelle
* Équilibreurs de charge accessibles sur Internet
* Passerelles de réseau virtuel (VPN/ER)
* Passerelles NAT
* Passerelles d’application
* Pare-feu Azure
* Hôte Bastion

Pour plus d’informations sur les adresses IP publiques, consultez [Adresses IP publiques](./public-ip-addresses.md) et [Créer, modifier ou supprimer une adresse IP publique Azure](./virtual-network-public-ip-address.md).

## <a name="public-ip-address-prefixes"></a>Préfixes d’adresse IP publique

Les préfixes d’adresse IP publique sont des plages réservées d’adresses IP dans Azure. Les préfixes d’adresses IP publiques sont constitués d’adresses IPv4 ou IPv6.  Dans les régions avec des zones de disponibilité, les préfixes d’adresses IP publiques peuvent être créés comme redondants dans une zone ou associés à une zone de disponibilité spécifique. Une fois le préfixe d’adresse IP publique créé, vous pouvez créer des adresses IP publiques.

Les tailles de préfixe IP public suivantes sont disponibles :

-  /28 (IPv4) ou /124 (IPv6) = 16 adresses
-  /29 (IPv4) ou /125 (IPv6) = 8 adresses
-  /30 (IPv4) ou /126 (IPv6) = 4 adresses
-  /31 (IPv4) ou /127 (IPv6) = 2 adresses

La taille de préfixe est spécifiée en tant que taille de masque CIDR (Classless Inter-Domain Routing).

Il n’existe pas de limites quant au nombre de préfixes créés dans un abonnement. Par conséquent, le nombre de plages que vous créez ne peut pas dépasser le nombre d’adresses IP publiques statiques autorisées dans votre abonnement. Pour plus d'informations, consultez [Limites Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Pour plus d’informations sur les préfixes d’adresse IP publique, consultez [Préfixe d’adresse IP publique](./public-ip-address-prefix.md) et [Créer, modifier ou supprimer une adresse IP publique](./manage-public-ip-address-prefix.md).

## <a name="private-ip-addresses"></a>Adresses IP privées

Les adresses IP privées permettent à des ressources de communiquer dans Azure. Azure attribue des adresses IP privées aux ressources à partir de la plage d’adresses du sous-réseau de réseau virtuel dans lequel se trouvent les ressources. Les adresses IP privées dans Azure sont statiques ou affectées dynamiquement.

Voici quelques-unes des ressources auxquelles vous pouvez associer une adresse IP privée :

* Machines virtuelles
* Équilibreurs de charge interne
* Passerelles d’application
* Instances Private Endpoint

Pour plus d’informations sur les adresses IP privées, consultez [Adresses IP privées](./private-ip-addresses.md).

## <a name="routing-preference"></a>Préférence de routage

Une préférence de routage Azure vous permet de choisir le mode d’acheminement de votre trafic entre Azure et Internet. Vous pouvez choisir de router le trafic via le réseau Microsoft ou le réseau du fournisseur de services Internet (Internet public). Vous pouvez choisir l’option de routage lors de la création d’une adresse IP publique. Par défaut, le trafic est acheminé via le réseau Microsoft mondial pour tous les services Azure. 

Les préférences de routage sont les suivantes :

* **Réseau Microsoft** – Le trafic d’entrée et de sortie voyage en grande partie sur le réseau Microsoft mondial. Ce routage est également connu sous l’appellation *routage selon le principe de la patate froide*.

* **Internet public (réseau de fournisseur de services Internet)** – Internet, le nouveau choix de routage, minimise le trajet sur le réseau Microsoft mondial et utilise le réseau de transit du fournisseur de services Internet pour acheminer votre trafic. Ce routage est également connu sous l’appellation *routage selon le principe de la patate chaude*.

Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](./routing-preference-overview.md).

## <a name="routing-preference-unmetered"></a>Préférence de routage non mesurée

La préférence de routage non mesurée est disponible pour les fournisseurs de réseau de distribution de contenu (CDN), dont les clients hébergent leur contenu d’origine dans Azure. Le service permet aux fournisseurs CDN d’établir une connexion de peering directe avec les routeurs Microsoft Global Network Edge à différents emplacements.

Le trafic réseau sortant de l’origine dans Azure, et destiné au fournisseur CDN, bénéficie de la connectivité directe.

* Les transferts de données du trafic sortant de vos ressources Azure qui sont routés via ces liens directs ne sont pas facturés.

* La connexion directe entre le fournisseur CDN et l’origine dans Azure offre des performances optimales, car il n’y a pas de sauts entre les deux. Cette connexion tire profit de la charge de travail CDN qui extrait fréquemment des données de l’origine.

Pour plus d’informations sur la préférence de routage non mesurée, consultez la section [Qu’est-ce qu’une préférence de routage non mesurée ?](./routing-preference-unmetered.md)

## <a name="next-steps"></a>Étapes suivantes

Commencez à créer des ressources des services IP :

- [Créer une adresse IP publique à l’aide du portail Azure](./create-public-ip-portal.md).
- [Créer un préfixe d’adresse IP publique à l’aide du portail Azure](./create-public-ip-prefix-portal.md).
- [Configurer une adresse IP privée pour une machine virtuelle à l’aide du portail Azure](./virtual-networks-static-private-ip-arm-pportal.md).
- [Configurer une préférence de routage pour une adresse IP publique à l’aide du portail Azure](./routing-preference-portal.md).