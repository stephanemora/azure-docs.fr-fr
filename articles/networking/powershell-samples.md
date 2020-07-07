---
title: Exemples Azure PowerShell - Mise en réseau
description: Exemples Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: ca6ac145db0536d3cf7e5bcc72a58d72101ab12a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81459115"
---
# <a name="azure-powershell-samples-for-networking"></a>Exemples Azure PowerShell pour la mise en réseau

Le tableau suivant contient des liens vers des scripts créés à l’aide d’Azure PowerShell.

| | |
|-|-|
|**Connectivité entre les ressources Azure**||
| [Créer un réseau virtuel pour les applications multiniveau](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic vers le sous-réseau frontal est limité à HTTP, tandis que le trafic vers le sous-réseau principal est limité à SQL, port 1433. |
| [Homologuer deux réseaux virtuels](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée et connecte deux réseaux virtuels dans la même région. |
| [Acheminer le trafic via une appliance virtuelle réseau](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée un réseau virtuel avec des sous-réseaux frontal et principal et une machine virtuelle en mesure d’acheminer le trafic entre les deux sous-réseaux. |
| [Filtrer le trafic réseau de machine virtuelle entrant et sortant](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic réseau entrant vers le sous-réseau frontal est limité à HTTP et HTTPS. Le trafic sortant vers Internet à partir du sous-réseau principal n’est pas autorisé. |
|**Équilibrage de charge et direction de trafic**||
| [Équilibrer le trafic sur les machines virtuelles pour la haute disponibilité](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Diriger le trafic dans plusieurs régions pour une haute disponibilité des applications](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Crée deux plans App Service, deux applications web, un profil Traffic Manager et deux points de terminaison Traffic Manager. |
| | |
