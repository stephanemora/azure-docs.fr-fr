---
title: Configurer des alertes sur les métriques de passerelle VPN Azure
description: Étapes pour configurer des alertes sur les mesures de la passerelle VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509909"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurer des alertes sur les métriques de passerelle VPN

Cet article vous permet de configurer des alertes sur les métriques de passerelle VPN Azure. Azure Monitor offre la possibilité de configurer des alertes pour les ressources Azure. Vous pouvez définir des alertes pour les passerelles de réseau virtuel du type « VPN ».


|**Mesure**   | **Unité** | **Granularité** | **Description** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Octets/s  | 5 minutes| Moyenne d’utilisation de la bande passante combinées de toutes les connexions de site à site sur la passerelle.     |
|**P2SBandwidth**| Octets/s  | 1 minute  | Moyenne d’utilisation de la bande passante combinées de toutes les connexions point à site sur la passerelle.    |
|**P2SConnectionCount**| Nombre  | 1 minute  | Nombre de connexions de point-to-site sur la passerelle.   |
|**TunnelAverageBandwidth** | Octets/s    | 5 minutes  | Utilisation de la bande passante moyenne de tunnels créés sur la passerelle. |
|**TunnelEgressBytes** | Octets | 5 minutes | Trafic sortant sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPackets** | Nombre | 5 minutes | Nombre de paquets sortants sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPacketDropTSMismatch** | Nombre | 5 minutes | Nombre de paquets sortants ignorés sur les tunnels dus à une incompatibilité de sélecteur de trafic. |
|**TunnelIngressBytes** | Octets | 5 minutes | Trafic entrant sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPackets** | Nombre | 5 minutes | Nombre de paquets entrants sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPacketDropTSMismatch** | Nombre | 5 minutes | Nombre de paquets entrants ignorés sur les tunnels dus à une incompatibilité de sélecteur de trafic. |


## <a name="setup"></a>Configurer des alertes Azure Monitor en fonction de mesures à l’aide du portail Azure

L’exemple suivant montre comment créera une alerte sur une passerelle pour :

- **Mesure :** TunnelAverageBandwidth
- **Condition :** La bande passante > 10 octets / seconde
- **Fenêtre :** 5 minutes
- **Action d’alerte :** Email



1. Accédez à la ressource de passerelle de réseau virtuel et sélectionnez **alertes** à partir de la **surveillance** onglet. Créez une règle d’alerte ou modifier une règle d’alerte existante.

   ![Sélections pour la création d’une règle d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "créer")

2. Sélectionnez votre passerelle VPN en tant que la ressource.

   ![Le bouton de sélection et la passerelle VPN dans la liste des ressources](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "sélectionnez")

3. Sélectionnez une métrique pour configurer l’alerte.

   ![Sélectionné métrique dans la liste des mesures](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "sélectionnez")
4. Configurer la logique de signal. Il existe trois composants :

    a. **Dimensions** : Si la mesure a des dimensions, vous pouvez sélectionner des valeurs de dimension spécifiques afin que l’alerte évalue uniquement les données de cette dimension. Ceux-ci sont facultatifs.

    b. **Condition** : Il s’agit de l’opération pour évaluer la valeur de métrique.

    c. **Temps**: Spécifier la granularité des données de mesure et la période de temps pour évaluer l’alerte.

   ![Détails de la configuration de signal logique](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "sélectionnez")

5. Pour afficher les règles configurées, sélectionnez **gérer les règles d’alerte**.

   ![Bouton pour la gestion des règles d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "sélectionnez")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les journaux de diagnostic de tunnel, consultez [configurer des alertes sur les journaux de diagnostic de passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
