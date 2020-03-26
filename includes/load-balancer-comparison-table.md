---
title: Fichier Include
description: Fichier Include
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78202522"
---
| | Standard Load Balancer | Basic Load Balancer |
| --- | --- | --- |
| [Taille du pool backend](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Prend en charge jusqu’à 1 000 instances. | Prend en charge jusqu’à 300 instances. |
| Points de terminaison du pool du serveur principal | Toutes les machines virtuelles ou tous les groupes de machines virtuelles identiques d’un seul réseau virtuel. | Machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques unique. |
| [Sondes d’intégrité](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportement en cas de panne de sonde d’intégrité](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Les connexions TCP restent actives sur une sonde d’instance en panne __et__ sur toutes les sondes en panne. | Les connexions TCP restent actives sur une sonde d’instance en panne. Toutes les connexions TCP s'arrêtent quand toutes les sondes sont en panne. |
| Zones de disponibilité | Front-ends redondants interzones et zonaux pour le trafic entrant et sortant. | Non disponible |
| Diagnostics | [Métriques multidimensionnelles Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Journaux d’activité Azure Monitor](../articles/load-balancer/load-balancer-monitor-log.md) |
| Ports HA | [Disponibles pour l’équilibreur de charge interne](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Non disponible |
| Sécurisé par défaut | Fermé aux flux entrants, sauf s’ils sont autorisés par un groupe de sécurité réseau. Notez que le trafic interne du réseau virtuel vers l’équilibreur de charge interne est autorisé. | Ouvertes par défaut. Groupe de sécurité réseau facultatif. |
| Règles de trafic sortant | [Configuration NAT déclarative de règles de trafic sortant](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Non disponible |
| Réinitialisation du protocole TCP en cas d’inactivité | [Disponible sur n’importe quelle règle](../articles/load-balancer/load-balancer-tcp-reset.md) | Non disponible |
| [Plusieurs serveurs frontaux](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrant et [sortant](../articles/load-balancer/load-balancer-outbound-connections.md) | Entrant uniquement |
| Opérations de gestion | La plupart des opérations < 30 secondes | Généralement 60 à 90 secondes et plus |
| Contrat SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Non disponible | 
