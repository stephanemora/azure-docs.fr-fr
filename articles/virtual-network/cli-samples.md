---
title: Exemples Azure CLI pour réseau virtuel
description: Découvrez les différents exemples de scripts que vous pouvez utiliser pour effectuer des tâches dans Azure CLI, notamment la création d’un réseau virtuel pour les applications multiniveaux.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 6f8d0bf40ac1a9c54796964b186f8871de58f201
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291947"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Exemples Azure CLI pour réseau virtuel

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de commandes Azure CLI :

| Script | Description |
|----|----|
| [Créer un réseau virtuel pour les applications multiniveau](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic vers le sous-réseau frontal est limité à HTTP et SSH, tandis que le trafic vers le sous-réseau principal est limité à MySQL, port 3306. |
| [Homologuer deux réseaux virtuels](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Crée et connecte deux réseaux virtuels dans la même région. |
| [Acheminer le trafic via une appliance virtuelle réseau](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Crée un réseau virtuel avec des sous-réseaux frontal et principal et une machine virtuelle en mesure d’acheminer le trafic entre les deux sous-réseaux. |
| [Filtrer le trafic réseau de machine virtuelle entrant et sortant](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic réseau entrant vers le sous-réseau frontal est limité à HTTP, HTTPS et SSH. Le trafic sortant vers Internet à partir du sous-réseau principal n’est pas autorisé. |
|[Configurer un réseau virtuel à double pile IPv4 + IPv6 avec Basic Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Déploie un réseau virtuel à double pile (IPv4 + IPv6) avec deux machines virtuelles et un équilibreur de charge de base Azure avec les adresses IP publiques IPv4 et IPv6. |
|[Configurer un réseau virtuel à double pile IPv4 + IPv6 avec Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Déploie un réseau virtuel à double pile (IPv4 + IPv6) avec deux machines virtuelles et un équilibreur de charge standard Azure avec les adresses IP publiques IPv4 et IPv6. |
|[Tutoriel : Créer et tester une passerelle NAT - Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Créer et vérifier une passerelle NAT en utilisant une machine virtuelle source et de destination. |