---
title: Analyse de la passerelle VPN Azure
description: Découvrez comment afficher les métriques de la passerelle VPN à l’aide de Azure Monitor.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/23/2021
ms.author: alzam
ms.openlocfilehash: a55c4ac84f218b4fe657f02cac9f10a4e9dc4a68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583942"
---
# <a name="monitoring-vpn-gateway"></a>Supervision de la passerelle VPN

Vous pouvez surveiller les passerelles VPN Azure à l’aide d’Azure Monitor. Cet article présente les métriques disponibles via le portail. Les métriques sont légères et peuvent prendre en charge des scénarios en quasi-temps réel, ce qui les rend utiles pour les alertes et la détection rapide de problèmes.


|**Mesure**   | **Unité** | **Granularité** | **Description** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| octets/s  | 5 minutes| Utilisation moyenne de la bande passante combinée pour toutes les connexions de site à site sur la passerelle.     |
|**P2SBandwidth**| octets/s  | 1 minute  | Utilisation moyenne de la bande passante combinée pour toutes les connexions de point à site sur la passerelle.    |
|**P2SConnectionCount**| Count  | 1 minute  | Nombre de connexions point à site sur la passerelle.   |
|**TunnelAverageBandwidth** | octets/s    | 5 minutes  | Utilisation moyenne de la bande passante pour les tunnels créés sur la passerelle. |
|**TunnelEgressBytes** | Octets | 5 minutes | Trafic sortant sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPackets** | Count | 5 minutes | Nombre de paquets sortants sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minutes | Nombre de paquets sortants ignorés sur les tunnels en raison d’une incompatibilité de sélecteur de trafic. |
|**TunnelIngressBytes** | Octets | 5 minutes | Trafic entrant sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPackets** | Count | 5 minutes | Nombre de paquets entrants sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minutes | Nombre de paquets entrants ignorés sur les tunnels en raison d’une incompatibilité de sélecteur de trafic. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Les étapes suivantes vous aident à localiser et à afficher les métriques :

1. Dans le portail, accédez à la ressource de la passerelle de réseau virtuel.
2. Sélectionnez **Vue d’ensemble** pour afficher les métriques d’entrée et de sortie du tunnel total.

   ![Sélections pour la création d’une règle d’alerte](./media/monitor-vpn-gateway/overview.png "Affichage")

3. Pour afficher les autres métriques répertoriées ci-dessus. Cliquez sur la section **Métriques** sous votre ressource de passerelle de réseau virtuel, puis sélectionnez la métrique dans la liste déroulante.

   ![Bouton Sélectionner et passerelle VPN dans la liste des ressources](./media/monitor-vpn-gateway/metrics.png "Sélectionnez")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les métriques de tunnel, consultez [Configurer des alertes sur les métriques de passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Pour configurer des alertes sur les journaux de ressources de tunnel, consultez [Configurer des alertes sur les journaux de ressources de la passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
