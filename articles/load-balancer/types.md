---
title: Références SKU Azure Load Balancer
description: Vue d’ensemble des références SKU Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: c7ca630b4a6a1bedeab21feacc22cd27a1a3ee7e
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734941"
---
# <a name="azure-load-balancer-skus"></a>Références SKU Azure Load Balancer

Azure Load Balancer a deux saveurs ou références SKU.

## <a name="sku-comparison"></a><a name="skus"></a> Comparaison des références SKU

L’équilibreur de charge prend en charge les références SKU De base et Standard. Ces références SKU présentent des différences en termes d’échelle de scénario, de fonctionnalités et de tarification. N’importe quel scénario possible avec l’équilibreur de charge De base peut être créé avec l’équilibreur de charge Standard.

Pour comparer les références SKU et comprendre leurs différences, consultez le tableau ci-dessous. Pour plus d’informations, consultez [Vue d’ensemble du niveau Standard d’Azure Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Microsoft recommande l’équilibreur de charge Standard.
Les machines virtuelles autonomes, les groupes à haute disponibilité et les groupes de machines virtuelles identiques peuvent uniquement être connectés à une référence SKU, jamais aux deux. Les références SKU de l’équilibreur de charge et des adresses IP publiques doivent correspondre quand vous les utilisez avec des adresses IP publiques. Les références SKU de l’équilibreur de charge et des adresses IP publiques ne sont pas mutables.

| | Standard Load Balancer | Basic Load Balancer |
| --- | --- | --- |
| [Taille du pool backend](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Prend en charge jusqu’à 1 000 instances. | Prend en charge jusqu’à 300 instances. |
| Points de terminaison du pool du serveur principal | Toutes les machines virtuelles ou tous les groupes de machines virtuelles identiques d’un seul réseau virtuel. | Machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques unique. |
| [Sondes d’intégrité](./load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportement en cas de panne de sonde d’intégrité](./load-balancer-custom-probe-overview.md#probedown) | Les connexions TCP restent actives sur une sonde d’instance en panne __et__ sur toutes les sondes en panne. | Les connexions TCP restent actives sur une sonde d’instance en panne. Toutes les connexions TCP s'arrêtent quand toutes les sondes sont en panne. |
| Zones de disponibilité | Front-ends redondants interzones et zonaux pour le trafic entrant et sortant. | Non disponible |
| Diagnostics | [Métriques multidimensionnelles Azure Monitor](./load-balancer-standard-diagnostics.md) | [Journaux d’activité Azure Monitor](./load-balancer-monitor-log.md) |
| Ports HA | [Disponibles pour l’équilibreur de charge interne](./load-balancer-ha-ports-overview.md) | Non disponible |
| Sécurisé par défaut | Fermé aux flux entrants, sauf s’ils sont autorisés par un groupe de sécurité réseau. Notez que le trafic interne du réseau virtuel vers l’équilibreur de charge interne est autorisé. | Ouvertes par défaut. Groupe de sécurité réseau facultatif. |
| Règles de trafic sortant | [Configuration NAT déclarative de règles de trafic sortant](./load-balancer-outbound-rules-overview.md) | Non disponible |
| Réinitialisation du protocole TCP en cas d’inactivité | [Disponible sur n’importe quelle règle](./load-balancer-tcp-reset.md) | Non disponible |
| [Plusieurs serveurs frontaux](./load-balancer-multivip-overview.md) | Entrant et [sortant](./load-balancer-outbound-connections.md) | Entrant uniquement |
| Opérations de gestion | La plupart des opérations < 30 secondes | Généralement 60 à 90 secondes et plus |
| Contrat SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Non disponible | 

Pour plus d’informations, consultez [Limites de Load balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Pour obtenir plus d’informations sur la référence SKU Standard de Load Balancer, consultez également la [présentation](load-balancer-standard-overview.md), la page relative à la [tarification](https://aka.ms/lbpricing) et la page relative au [SLA](https://aka.ms/lbsla).

## <a name="next-steps"></a>Étapes suivantes

- Pour bien démarrer avec les équilibreurs de charge, consultez [Créer un équilibreur de charge standard public](quickstart-load-balancer-standard-public-portal.md).
- Découvrez comment utiliser [Standard Load Balancer et les zones de disponibilité](load-balancer-standard-availability-zones.md).
- Découvrez les [sondes d’intégrité](load-balancer-custom-probe-overview.md).
- Découvrez comment utiliser [Load Balancer pour les connexions sortantes](load-balancer-outbound-connections.md).
- Découvrez [Load Balancer Standard avec les règles d’équilibrage de charge des ports HA](load-balancer-ha-ports-overview.md).
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/security-overview.md).
