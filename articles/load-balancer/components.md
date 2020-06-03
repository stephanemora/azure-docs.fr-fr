---
title: Composants Azure Load Balancer
description: Vue d’ensemble des composants Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 84857315e4b6b4375ed5b78520b4c6ff0d66751a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684991"
---
# <a name="azure-load-balancer-components"></a>Composants Azure Load Balancer

Azure Load Balancer comprend quelques composants clés, qui peuvent être configurés dans un abonnement par le biais du Portail Azure, d’Azure CLI, d’Azure PowerShell ou de modèles.

## <a name="frontend-ip-configuration"></a>Configuration IP front-end <a name = "frontend-ip-configurations"></a>

Adresse IP de l’équilibreur de charge Azure Load Balancer. Il s’agit du point de contact pour les clients. Ces adresses IP peuvent être de deux types :

- **Adresse IP publique**
- **Adresse IP privée**

La nature de l’adresse IP détermine le **type** d’équilibreur de charge créé. La sélection d’une adresse IP privée crée un équilibreur de charge interne. La sélection d’une adresse IP publique crée un équilibreur de charge public.

|  | Équilibreur de charge public  | Équilibreur de charge interne |
| ---------- | ---------- | ---------- |
| Configuration d’adresses IP frontales| Adresse IP publique | Adresse IP privée|
| Description | Un équilibreur de charge public mappe l’adresse IP publique et le port du trafic entrant à l’adresse IP privée et au port de la machine virtuelle. L’équilibreur de charge mappe le trafic dans l’autre sens pour le trafic de réponse provenant de la machine virtuelle. Vous pouvez distribuer des types de trafic donnés entre plusieurs machines virtuelles ou services en appliquant des règles d’équilibrage de charge. Par exemple, vous pouvez répartir la charge du trafic des requêtes web entre plusieurs serveurs web.| Un équilibreur de charge interne distribue le trafic entre les ressources qui se trouvent à l’intérieur d’un réseau virtuel. Azure limite l’accès aux adresses IP front-end d’un réseau virtuel qui sont soumises à l’équilibrage de charge. Les adresses IP du serveur frontal et les réseaux virtuels ne sont jamais directement exposés à un point de terminaison Internet. Les applications métier internes s’exécutent dans Azure et sont accessibles à partir d’Azure ou à partir des ressources locales. |
| Références SKU prises en charge | De base, Standard | De base, Standard |

![Exemple d’équilibreur de charge hiérarchisé](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>Pool principal

Groupe de machines virtuelles ou d’instances dans un groupe de machines virtuelles identiques qui distribuent la requête entrante. Pour une mise à l’échelle économique visant à répondre à des volumes élevés de trafic entrant, il est généralement recommandé d’ajouter davantage d’instances au pool de back-ends.

L’équilibreur de charge se reconfigure instantanément par le biais d’une reconfiguration automatique quand vous effectuez un scale-up ou un scale-down des instances. Quand vous ajoutez ou supprimez des machines virtuelles du pool de back-ends, l’équilibreur de charge est reconfiguré sans opération supplémentaire. L’étendue du pool de back-ends est toute machine virtuelle du réseau virtuel.

Quand vous envisagez la conception de votre pool de back-ends, concevez le moins de ressources de pool de back-ends individuelles pour optimiser la longueur des opérations de gestion. Il n’existe aucune différence de performances ou de mise à l’échelle du plan de données.

## <a name="health-probes"></a>Sondes d’intégrité

Une sonde d’intégrité sert à déterminer l’état d’intégrité des instances du pool de back-ends. Lorsque vous créez un équilibreur de charge, vous devez configurer une sonde d’intégrité lui permettant de savoir si une instance est saine et d’acheminer le trafic vers celle-ci.

Vous pouvez définir le seuil de défaillance sur le plan de l’intégrité pour vos sondes d’intégrité. Quand une sonde ne répond pas, l’équilibreur de charge n’envoie plus de nouvelles connexions aux instances non saines. Un échec de la sonde n’affecte pas les connexions existantes. La connexion se poursuit :

- Jusqu’à ce que l’application termine le flux
- Jusqu’à ce que le délai d’inactivité soit atteint
- Jusqu’à ce que la machine virtuelle s’arrête

Azure Load Balancer fournit différents types de sondes d’intégrité pour les points de terminaison : TCP, HTTP et HTTPS.

L’équilibreur de charge de base ne prend pas en charge les sondes HTTPS. Il ferme toutes les connexions TCP (y compris les connexions établies).

## <a name="load-balancing-rules"></a>Règles d’équilibrage de charge

Une règle d’équilibrage de charge sert à définir la manière dont le trafic entrant est distribué à **toutes** les instances du pool de back-ends. Elle fait correspondre une configuration IP front-end et un port donnés à plusieurs adresses IP back-end et ports.

Par exemple, si vous souhaitez que le trafic sur le port 80 (ou un autre port) de votre adresse IP front-end soit routé vers le port 80 de toutes vos instances back-end, vous devez utiliser une règle d’équilibrage de charge.

## <a name="inbound-nat-rules"></a>Règles NAT entrantes

Une règle NAT de trafic entrant transfère le trafic entrant envoyé à une combinaison d’adresse IP front-end et de port sélectionnée à une machine virtuelle ou à une instance **spécifique** du pool de back-ends. Ce réacheminement de port est accompli à l’aide de la même distribution basée sur le hachage que l’équilibrage de charge.

Par exemple, si vous souhaitez que les sessions RDP (Remote Desktop Protocol) ou SSH (Secure Shell) séparent les instances de machine virtuelle d’un pool de back-ends, plusieurs points de terminaison internes peuvent être mappés à des ports sur la même adresse IP front-end. Les adresses IP front-end peuvent être utilisées pour administrer à distance vos machines virtuelles sans serveur de rebond supplémentaire.

## <a name="outbound-rules"></a>Règles de trafic sortant

Une règle de trafic sortant configure la traduction d’adresses réseau (NAT) du trafic sortant pour toutes les machines virtuelles ou instances identifiées par le pool de back-ends. Les instances du back-end peuvent ainsi communiquer (vers l’extérieur) sur Internet ou vers d’autres points de terminaison.

L’équilibreur de charge de base ne prend pas en charge les règles de trafic sortant.

## <a name="next-steps"></a>Étapes suivantes

- Pour bien démarrer avec les équilibreurs de charge, consultez [Créer un équilibreur de charge standard public](quickstart-load-balancer-standard-public-portal.md).
- En savoir plus sur [Azure Load Balancer](load-balancer-overview.md).
- Découvrez les [adresses IP publiques](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).
- Découvrez les [adresses IP privées](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).
- Découvrez comment utiliser [Standard Load Balancer et les zones de disponibilité](load-balancer-standard-availability-zones.md).
- En savoir plus sur les [Diagnostics Load Balancer Standard](load-balancer-standard-diagnostics.md).
- En savoir plus sur la [réinitialisation TCP au terme du délai d’inactivité](load-balancer-tcp-reset.md).
- Découvrez [Load Balancer Standard avec les règles d’équilibrage de charge des ports HA](load-balancer-ha-ports-overview.md).
- Découvrez comment utiliser [Load Balancer avec plusieurs configurations IP front-end](load-balancer-multivip-overview.md).
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/security-overview.md).
- Découvrez les [types de sondes](load-balancer-custom-probe-overview.md#types).
- Découvrez-en plus sur les [limites des équilibreurs de charge](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Découvrez l’utilisation du [réacheminement de port](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Découvrez-en plus sur les [règles de trafic sortant des équilibreurs de charge](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
