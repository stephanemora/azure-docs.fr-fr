---
title: Informations de référence sur la supervision des données Load Balancer
titleSuffix: Azure Load Balancer
description: Matériel de référence important nécessaire pour superviser Load Balancer
author: asudbring
ms.topic: reference
ms.author: allensu
ms.service: load-balancer
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: ac5a7394b13ac34ad9d398458627765316369de9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296505"
---
# <a name="monitoring-load-balancer-data-reference"></a>Informations de référence sur les données de supervision de l’équilibreur de charge

Pour plus d’informations sur la collecte et l’analyse des données de supervision concernant Load Balancer, consultez [Supervision de Load Balancer](monitor-load-balancer.md).

## <a name="metrics"></a>Mesures

### <a name="load-balancer-metrics"></a>Métriques de l’équilibreur de charge 

| Métrique | Type de ressource | Description | Agrégation recommandée |
| ------ | ------------- | ----------- | ----------------------- |
| Disponibilité du chemin d’accès aux données | Équilibreur de charge interne et public |  Load Balancer Standard teste en continu le chemin de données d’une région vers le serveur frontal de l’équilibreur de charge, jusqu’à la pile SDN qui prend en charge votre machine virtuelle. Tant que les instances saines restent, la mesure suit le même chemin que le trafic à charge équilibrée de vos applications. Le chemin de données utilisé par vos clients est également validé. La mesure est invisible pour votre application et n’interfère pas avec les autres opérations. | Average |
| État de la sonde d’intégrité | Équilibreur de charge interne et public | Load Balancer Standard utilise un service de détection d’intégrité distribué qui surveille l’intégrité du point de terminaison de votre application en fonction de vos paramètres de configuration. Cette métrique fournit un agrégat ou une vue filtrée par point de terminaison de chaque point de terminaison d’instance dans le pool de l’équilibreur de charge. Vous pouvez observer comment Load Balancer voit l’intégrité de votre application comme indiqué par votre configuration de sonde d’intégrité. | Average |
| Nombre de SYN (synchroniser) | Équilibreur de charge interne et public | Load Balancer Standard utilise un service de détection d’intégrité distribué qui surveille l’intégrité du point de terminaison de votre application en fonction de vos paramètres de configuration. Cette métrique fournit un agrégat ou une vue filtrée par point de terminaison de chaque point de terminaison d’instance dans le pool de l’équilibreur de charge. Vous pouvez observer comment Load Balancer voit l’intégrité de votre application comme indiqué par votre configuration de sonde d’intégrité. | Average |
| Nombre de connexions SNAT | Équilibreur de charge public | Load Balancer Standard indique le nombre de flux sortants usurpés sur le serveur frontal d’adresse IP public. Les ports de traduction d'adresses réseau source (SNAT) constituent une ressource épuisable. Cette métrique peut donner une idée de l’importance du rôle joué par SNAT dans votre application pour les flux sortants. Les compteurs relatifs aux flux SNAT sortants réussis et mis en échec sont indiqués et peuvent être utilisés pour comprendre l’intégrité de vos flux sortants et résoudre les problèmes associés. | SUM |
| Ports SNAT alloués | Équilibreur de charge public | Standard Load Balancer indique le nombre de ports SNAT alloués par instance de back-end. | Moyenne |
| Ports SNAT utilisés | Équilibreur de charge public | Standard Load Balancer indique le nombre de ports SNAT utilisés par instance de back-end. | Average |
| Nombre d’octets | Équilibreur de charge interne et public | Load Balancer Standard indique les données traitées par serveur frontal. Vous pouvez remarquer que les octets ne sont pas répartis de manière égale entre les instances du serveur principal. C’est normal, car l’algorithme d’Azure Load Balancer est basé sur les flux. | SUM |
| Nombre de paquets | Équilibreur de charge interne et public | Load Balancer Standard indique les paquets traités par serveur frontal. | SUM |

Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor pour l’équilibreur de charge](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkloadbalancers).

## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Load Balancer a les dimensions suivantes associées à ses métriques.

| Nom de la dimension | Description |
| -------------- | ----------- |
| Adresse IP du front-end | Adresse IP de front-end de la ou des règles d’équilibrage de charge appropriées |
| Port front-end | Port front-end de la ou des règles d’équilibrage de charge appropriées | 
| Adresse IP de back-end | Adresse IP de back-end de la ou des règles d’équilibrage de charge appropriées |
| Port du back-end | Port de back-end de la ou des règles d’équilibrage de charge appropriées |
| Type de protocole | Protocole de la règle d’équilibrage de charge appropriée, qui peut être TCP ou UDP |
| Sens | Sens du trafic. Il peut s’agir d’un trafic entrant ou sortant. | 
| Nom du groupe du cluster du membre | État de la connexion SNAT : en attente, réussite ou échec | 

## <a name="resource-logs"></a>Journaux d’activité de ressources

Les journaux de ressources ne sont pas pris en charge par Azure Load Balancer.

## <a name="azure-monitor-logs-tables"></a>Tables des journaux Azure Monitor
### <a name="diagnostics-tables"></a>Tables de diagnostic

Les tables de diagnostic ne sont pas prises en charge par Azure Load Balancer.
## <a name="activity-log"></a>Journal d’activité

Le tableau suivant liste les opérations relatives à Load Balancer qui peuvent être créées dans le journal d’activité.

| Opération | Description |
| --- | --- |
| Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/write | Crée un équilibrage de charge ou met à jour un équilibrage de charge existant. |
| Microsoft.Network/loadBalancers/delete | Supprime un équilibrage de charge. |
| Microsoft.Network/loadBalancers/backendAddressPools/queryInboundNatRulePortMapping/action | Interroge le mappage des ports de la règle NAT de trafic entrant. |
| Microsoft.Network/loadBalancers/backendAddressPools/read | Obtient une définition de pool d’adresses principales d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/backendAddressPools/write | Crée ou met à jour un pool d’adresses principal d’équilibreur de charge |
| Microsoft.Network/loadBalancers/backendAddressPools/delete | Supprime un pool d’adresses principal d’équilibreur de charge |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. Impossible à alerter. |
| Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | Répertorie les adresses principales du pool d’adresses principal Azure Load Balancer |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obtient une définition de configuration d’adresse IP frontale d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Joint une configuration d’adresse IP frontale d’équilibrage de charge Impossible à alerter. |
| Microsoft.Network/loadBalancers/inboundNatPools/read | Obtient une définition de pool NAT entrant d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joint un pool NAT entrant d’équilibrage de charge. Impossible à alerter. |
| Microsoft.Network/loadBalancers/inboundNatRules/read | Obtient une définition de règle NAT entrante d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/inboundNatRules/write | Crée une règle NAT entrante d’équilibrage de charge ou met à jour une règle NAT entrante d’équilibrage de charge existante. |
| Microsoft.Network/loadBalancers/inboundNatRules/delete | Supprime une règle NAT entrante d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joint une règle nat de trafic entrant d’équilibrage de charge. Impossible à alerter. |
| Microsoft.Network/loadBalancers/loadBalancingRules/read | Obtient une définition de règle d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/networkInterfaces/read | Obtient les références à toutes les interfaces réseau sous un équilibrage de charge. |
| Microsoft.Network/loadBalancers/outboundRules/read | Obtient une définition de règle sortante d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/probes/read | Obtient une sonde d’équilibrage de charge. |
| Microsoft.Network/loadBalancers/probes/join/action | Autorise l’utilisation des sondes d’un équilibreur de charge. Par exemple, avec cette autorisation, la propriété healthProbe du groupe de machines virtuelles identiques peut faire référence à la sonde. Impossible à alerter. |
| Microsoft.Network/loadBalancers/virtualMachines/read | Obtient les références à toutes les machines virtuelles sous un équilibrage de charge. |

Pour plus d’informations sur le schéma des entrées du journal d’activité, consultez [Schéma du journal d’activité](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Voir aussi

- Consultez [Supervision d’Azure Load Balancer](./monitor-load-balancer.md) pour obtenir une description de la supervision d’Azure Load Balancer.