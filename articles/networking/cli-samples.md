---
title: Exemples d’interface de ligne de commande Azure - mise en réseau
description: Exemples d’interface de ligne de commande Azure
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 84754a61bfe9537e928759aefbcb5dcddce33089
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81457958"
---
# <a name="azure-cli-samples-for-networking"></a>Exemples d’interface de ligne de commande Azure pour la mise en réseau

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|-|-|
|**Connectivité entre les ressources Azure**||
| [Créer un réseau virtuel pour les applications multiniveau](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic vers le sous-réseau frontal est limité à HTTP et SSH, tandis que le trafic vers le sous-réseau principal est limité à MySQL, port 3306. |
| [Homologuer deux réseaux virtuels](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée et connecte deux réseaux virtuels dans la même région. |
| [Acheminer le trafic via une appliance virtuelle réseau](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée un réseau virtuel avec des sous-réseaux frontal et principal et une machine virtuelle en mesure d’acheminer le trafic entre les deux sous-réseaux. |
| [Filtrer le trafic réseau de machine virtuelle entrant et sortant](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic réseau entrant vers le sous-réseau frontal est limité à HTTP, HTTPS et SSH. Le trafic sortant vers Internet à partir du sous-réseau principal n’est pas autorisé. |
|**Équilibrage de charge et direction de trafic**||
| [Équilibrer la charge de plusieurs sites web sur des machines virtuelles](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée deux machines virtuelles avec plusieurs configurations IP, jointes à un groupe à haute disponibilité Azure, accessible via Azure Load Balancer. |
| [Diriger le trafic dans plusieurs régions pour une haute disponibilité des applications](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Crée deux plans App Service, deux applications web, un profil Traffic Manager et deux points de terminaison Traffic Manager. |
| | |
