---
title: Configurer des alertes sur les métriques de passerelle VPN Azure
description: Étapes pour configurer des alertes sur les métriques de passerelle VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65509909"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurer des alertes sur les métriques de passerelle VPN

Cet article vous aide à configurer des alertes sur les métriques de passerelle VPN Azure. Azure Monitor permet de configurer des alertes pour les ressources Azure. Vous pouvez définir des alertes pour les passerelles de réseau virtuel de type « VPN ».


|**Mesure**   | **Unité** | **Granularité** | **Description** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| octets/s  | 5 minutes| Utilisation moyenne de la bande passante combinée pour toutes les connexions de site à site sur la passerelle.     |
|**P2SBandwidth**| octets/s  | 1 minute  | Utilisation moyenne de la bande passante combinée pour toutes les connexions de point à site sur la passerelle.    |
|**P2SConnectionCount**| Nombre  | 1 minute  | Nombre de connexions point à site sur la passerelle.   |
|**TunnelAverageBandwidth** | octets/s    | 5 minutes  | Utilisation moyenne de la bande passante pour les tunnels créés sur la passerelle. |
|**TunnelEgressBytes** | Octets | 5 minutes | Trafic sortant sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPackets** | Nombre | 5 minutes | Nombre de paquets sortants sur les tunnels créés sur la passerelle.   |
|**TunnelEgressPacketDropTSMismatch** | Nombre | 5 minutes | Nombre de paquets sortants ignorés sur les tunnels en raison d’une incompatibilité de sélecteur de trafic. |
|**TunnelIngressBytes** | Octets | 5 minutes | Trafic entrant sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPackets** | Nombre | 5 minutes | Nombre de paquets entrants sur les tunnels créés sur la passerelle.   |
|**TunnelIngressPacketDropTSMismatch** | Nombre | 5 minutes | Nombre de paquets entrants ignorés sur les tunnels en raison d’une incompatibilité de sélecteur de trafic. |


## <a name="setup"></a>Configurer des alertes Azure Monitor en fonction de métriques à l’aide du portail Azure

L’exemple suivant montre comment créera une alerte sur une passerelle pour :

- **Mesure :** TunnelAverageBandwidth
- **Condition :** Bande passante > 10 octets/seconde
- **Fenêtre :** 5 minutes
- **Action d’alerte :** Email



1. Accédez à la ressource de passerelle de réseau virtuel et sélectionnez **Alertes** dans l’onglet **Surveillance**. Ensuite, créez une règle d’alerte ou modifiez une règle d’alerte existante.

   ![Sélections pour la création d’une règle d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Créer")

2. Sélectionnez votre passerelle VPN en tant que ressource.

   ![Bouton de sélection et passerelle VPN dans la liste des ressources](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Sélectionner")

3. Sélectionnez une métrique à configurer pour l’alerte.

   ![Métrique sélectionnée dans la liste des métriques](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Sélectionner")
4. Configurez la logique du signal. Il existe trois composants :

    a. **Dimensions** : si la métrique a des dimensions, vous pouvez sélectionner des valeurs de dimension spécifiques afin que l’alerte évalue uniquement les données de cette dimension. Cela est facultatif.

    b. **Condition** : il s’agit de l’opération visant à évaluer la valeur de métrique.

    c. **Temps** : spécifiez la granularité des données de métrique et la période pour laquelle évaluer l’alerte.

   ![Détails de la configuration de logique de signal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Sélectionner")

5. Pour afficher les règles configurées, sélectionnez **Gérer les règles d’alerte**.

   ![Bouton pour la gestion des règles d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Sélectionner")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les journaux de diagnostic de tunnel, consultez [Configurer des alertes sur les événements du journal de diagnostic à partir de la passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
