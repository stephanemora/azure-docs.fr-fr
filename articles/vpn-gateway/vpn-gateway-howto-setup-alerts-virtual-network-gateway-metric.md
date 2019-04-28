---
title: Comment configurer des alertes sur les métriques de passerelle VPN Azure
description: Étapes pour configurer des alertes sur les mesures de la passerelle VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769465"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>Définir des alertes sur les mesures de la passerelle VPN

Cet article vous permet de configurer des alertes pour les métriques de passerelle VPN. Azure monitor fournit la possibilité de configurer des alertes pour les ressources Azure. Alertes peuvent être configurés pour les passerelles de réseau virtuel de type « VPN ».


|**Mesure**   | **Unité** | **Granularité** | **Description** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Octets/s  | 5 minutes| Moyenne d’utilisation de la bande passante combinées de toutes les connexions de site à site sur la passerelle.     |
|**P2SBandwidth**| Octets/s  | 1 minute  | Moyenne d’utilisation de la bande passante combinées de toutes les connexions point à site sur la passerelle.    |
|**P2SConnectionCount**| Nombre  | 1 minute  | Nombre de P2S des connexions sur la passerelle.   |
|**TunnelAverageBandwidth** | Octets/s    | 5 minutes  | Utilisation de la bande passante moyenne de tunnels créés sur la passerelle. |
|**TunnelEgressBytes** | Octets | 5 minutes | Trafic sortant sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPackets** | Nombre | 5 minutes | Nombre de paquets sortants sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPacketDropTSMismatch** | Nombre | 5 minutes | Nombre de paquets sortants ignorés sur les tunnels dus à une incompatibilité de TS. |
|**TunnelIngressBytes** | Octets | 5 minutes | Trafic entrant sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPackets** | Nombre | 5 minutes | Nombre de paquets entrants sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPacketDropTSMismatch** | Nombre | 5 minutes | Nombre de paquets entrants ignorés sur les tunnels dus à une incompatibilité de TS. |


## <a name="setup"></a>Configurer des alertes Azure Monitor basées sur les mesures à l’aide du portail

Les étapes de l’exemple ci-dessous crée une alerte sur une passerelle pour : <br>

**Mesure :** Bande passante moyenne de tunnel <br>
**Condition :** la bande passante > 10 octets / seconde <br>
**Fenêtre :** 5 minutes <br>
**Action d’alerte :** Email <br>



1. Accédez à la ressource de passerelle de réseau virtuel et sélectionnez « Alertes » dans l’onglet analyse, puis créer une règle d’alerte ou modifier une règle d’alerte existante.

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "créer")

2. Sélectionnez votre passerelle VPN en tant que la ressource.

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "sélectionnez")

3. Sélectionnez une métrique pour configurer l’alerte ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "sélectionnez")
4. Configurer la logique de signal. Il existe trois composants de logique du signal :

    a. Dimensions : Si la mesure a des dimensions, les valeurs de dimension spécifique peuvent être sélectionnés afin que l’alerte évalue uniquement les données de cette dimension. Ceux-ci sont facultatifs.<br>
    b. Condition : L’opération pour évaluer la valeur de métrique.<br>
    c. Heure : Spécifier la granularité des données de mesure et la période de temps pour évaluer l’alerte sur.<br>

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "sélectionnez")

5. Pour afficher les règles configurées, cliquez sur « Gérer les règles d’alerte » ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "sélectionnez")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les journaux de diagnostic de tunnel, consultez [comment configurer des alertes sur les journaux de diagnostic de passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
