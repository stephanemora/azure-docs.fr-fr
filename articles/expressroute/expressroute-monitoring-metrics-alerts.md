---
title: 'Azure ExpressRoute : Supervision, métriques et alertes'
description: Découvrez la surveillance, les métriques et les alertes Azure ExpressRoute à l’aide d’Azure Monitor, le guichet unique pour l’ensemble des métriques, alertes et journaux de diagnostic dans Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091564"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Supervision, métriques et alertes ExpressRoute

Cet article vous aide à comprendre la supervision, les métriques et les alertes ExpressRoute à l’aide d’Azure Monitor. Azure Monitor est un emplacement centralisé pour toutes les métriques, alertes et journaux de diagnostic dans Azure.
 
>[!NOTE]
>L’utilisation des **métriques classiques** n’est pas recommandée.
>

## <a name="expressroute-metrics"></a>Métriques ExpressRoute

Pour afficher les **métriques**, accédez à la page *Azure Monitor*, puis cliquez sur *Métriques*. Pour voir les métriques **ExpressRoute**, filtrez les *circuits ExpressRoute* par type de ressource. Pour afficher les métriques **Global Reach**, filtrez les *circuits ExpressRoute* par type de ressource, puis sélectionnez une ressource de circuit ExpressRoute où Global Reach est activé. Pour afficher les métriques **ExpressRoute Direct**, filtrez les *ports ExpressRoute* par type de ressource. 

Une fois qu’une métrique est sélectionnée, l’agrégation par défaut est appliquée. Si vous le souhaitez, vous pouvez appliquer une division pour afficher les métriques avec différentes dimensions.

### <a name="available-metrics"></a>Métriques disponibles

|**Mesure**|**Catégorie**|**Dimension(s)**|**Fonctionnalité(s)**|
| --- | --- | --- | --- |
|Disponibilité ARP|Disponibilité|<ui><li>Pair (routeur ExpressRoute principal/secondaire)</ui></li><ui><li> Type d’appairage (privé/public/Microsoft)</ui></li>|ExpressRoute|
|Disponibilité du protocole BGP|Disponibilité|<ui><li> Pair (routeur ExpressRoute principal/secondaire)</ui></li><ui><li> Type d’appairage</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafic|<ui><li> Type d’appairage (ExpressRoute)</ui></li><ui><li>Liaison (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>Connexion de passerelle ExpressRoute</ui></li>|
|BitsOutPerSecond|Trafic| <ui><li>Type d’appairage (ExpressRoute)</ui></li><ui><li> Liaison (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>Connexion de passerelle ExpressRoute</ui></li>|
|Utilisation du processeur|Performances| <ui><li>Instance</ui></li>|Passerelle de réseau virtuel ExpressRoute|
|Paquets par seconde|Performances| <ui><li>Instance</ui></li>|Passerelle de réseau virtuel ExpressRoute|
|Nombre d’itinéraires publiés pour l’homologue |Disponibilité| <ui><li>Instance</ui></li>|Passerelle de réseau virtuel ExpressRoute|
|Nombre d’itinéraires appris de l’homologue |Disponibilité| <ui><li>Instance</ui></li>|Passerelle de réseau virtuel ExpressRoute|
|Fréquence de changement d’itinéraire |Disponibilité| <ui><li>Instance</ui></li>|Passerelle de réseau virtuel ExpressRoute|
|Nombre de machines virtuelles dans le réseau virtuel |Disponibilité| N/A |Passerelle de réseau virtuel ExpressRoute|
|GlobalReachBitsInPerSecond|Trafic|<ui><li>Skey de circuit appairé (clé de service)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Trafic|<ui><li>Skey de circuit appairé (clé de service)</ui></li>|Global Reach|
|AdminState|Connectivité physique|Lien|ExpressRoute Direct|
|LineProtocol|Connectivité physique|Lien|ExpressRoute Direct|
|RxLightLevel|Connectivité physique|<ui><li>Liaison</ui></li><ui><li>Couloir</ui></li>|ExpressRoute Direct|
|TxLightLevel|Connectivité physique|<ui><li>Liaison</ui></li><ui><li>Couloir</ui></li>|ExpressRoute Direct|
>[!NOTE]
>L’utilisation des métriques *GlobalGlobalReachBitsInPerSecond* et *GlobalGlobalReachBitsOutPerSecond* sera visible uniquement si au moins une connexion Global Reach est établie.
>

## <a name="circuits-metrics"></a>Métriques des circuits

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits entrants et sortants : métriques sur l’ensemble des peerings

Vous pouvez voir les métriques sur l’ensemble des peerings sur un circuit ExpressRoute donné.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="métriques de circuit":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits entrants et sortants : métriques par peering

Vous pouvez afficher des mesures pour le peering privé, public et Microsoft en bits par seconde.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="métriques par peering":::

### <a name="bgp-availability---split-by-peer"></a>Disponibilité du protocole BGP : découpage par pair  

Vous pouvez consulter la disponibilité du protocole BGP en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord présente la session BGP principale en haut pour le peering privé et la session BGP secondaire en bas pour le peering privé. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilité du protocole BGP par pair":::

### <a name="arp-availability---split-by-peering"></a>Disponibilité du protocole ARP : découpage par peering  

Vous pouvez consulter la disponibilité du protocole [ARP](./expressroute-troubleshooting-arp-resource-manager.md) en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord présente la session ARP de peering privé en haut sur les deux pairs, mais la session complète en bas pour le peering Microsoft sur les peerings. L’agrégation par défaut (moyenne) a été utilisée sur les deux pairs.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilité du protocole ARP par pair":::

## <a name="expressroute-direct-metrics"></a>Métriques de ExpressRoute Direct

### <a name="admin-state---split-by-link"></a>État d’administration – Lien Diviser par

Vous pouvez afficher l’état d’administration pour chaque lien de la paire de ports directs ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="État de l'administrateur ER Direct":::

### <a name="bits-in-per-second---split-by-link"></a>Bits entrants par seconde – Lien Diviser par

Vous pouvez afficher les bits entrants par seconde sur les deux liens de la paire de ports ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Bits ER Direct entrants par seconde":::

### <a name="bits-out-per-second---split-by-link"></a>Bits sortants par seconde – Lien Diviser par

Vous pouvez également afficher les bits sortants par seconde sur les deux liens de la paire de ports ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Bits ER Direct sortants par seconde":::

### <a name="line-protocol---split-by-link"></a>Protocole de ligne – Lien Diviser par

Vous pouvez afficher le protocole de ligne sur chaque lien de la paire de ports ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocole de ligne ER Direct":::

### <a name="rx-light-level---split-by-link"></a>Niveau d’éclairage de réception – Lien Diviser par

Vous pouvez afficher le niveau d’éclairage de réception (niveau d’éclairage que le port ExpressRoute Direct **reçoit**) pour chaque port. Les niveaux d’éclairage de réception sains s’inscrivent généralement dans une plage de -10 à 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Niveau d'éclairage de réception de ligne ER Direct":::

### <a name="tx-light-level---split-by-link"></a>Niveau d’éclairage de transmission – Lien Diviser par

Vous pouvez afficher le niveau d’éclairage de transmission (niveau d’éclairage que le port ExpressRoute Direct **transmet**) pour chaque port. Les niveaux d’éclairage de transmission sains s’inscrivent généralement dans une plage de -10 à 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Niveau d'éclairage de transmission de ligne ER Direct":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Métriques de passerelle de réseau virtuel ExpressRoute

### <a name="cpu-utilization---split-instance"></a>Utilisation du processeur - Instance fractionnée

Vous pouvez consulter l'utilisation du processeur des instances de la passerelle.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Processeur fractionné":::

### <a name="packets-per-second---split-by-instance"></a>Paquets par seconde - Fractionner par instance

Vous pouvez consulter les paquets par seconde qui traversent la passerelle.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Paquets par seconde - fractionner":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Nombre d’itinéraires publiés pour l’homologue – Fractionné par instance

Vous pouvez afficher le nombre d’itinéraires publiés sur le circuit ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Nombre d’itinéraires publiés pour l’homologue":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Nombre d’itinéraires appris de l’homologue – Fractionné par instance

Vous pouvez afficher le nombre d’itinéraires reçus du circuit ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Nombre d’itinéraires appris de l’homologue":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Fréquence de changement d’itinéraire – Fractionnée par instance

Vous pouvez afficher la fréquence de changement d’itinéraire sur la passerelle.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Fréquence des itinéraires modifiés":::

### <a name="number-of-vms-in-the-virtual-network"></a>Nombre de machines virtuelles dans le réseau virtuel

Vous pouvez afficher le nombre de machines virtuelles dans le réseau virtuel.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Nombre de machines virtuelles dans le réseau virtuel":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connexions de passerelle ExpressRoute en bits/secondes

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="connexions de passerelle":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertes pour les connexions de passerelle ExpressRoute

1. Pour configurer des alertes, accédez à **Azure Monitor**, puis sélectionnez **Alertes**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Cliquez sur **+Sélectionner la cible** et sélectionnez la ressource de connexion de passerelle ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="cible":::
3. Définissez les détails de l’alerte.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="groupe d’actions":::
4. Définissez et ajoutez le groupe d’actions.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="ajouter un groupe d'actions":::

## <a name="alerts-based-on-each-peering"></a>Alertes basées sur chaque peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="chaque peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurer des alertes pour les journaux d’activité sur des circuits

Dans les **Critères d’alerte**, vous pouvez sélectionner le Type de signal **Journal d’activité** puis sélectionner le Signal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="journaux d'activité":::

## <a name="additional-metrics-in-log-analytics"></a>Métriques supplémentaires dans Log Analytics

Vous pouvez également afficher les métriques ExpressRoute en accédant à votre ressource de circuit ExpressRoute et en sélectionnant l’onglet *Journaux*. Pour toutes les métriques que vous interrogez, la sortie contiendra les colonnes ci-dessous.

|**Colonne**|**Type**|**Description**|
| --- | --- | --- |
|TimeGrain|string|PT1M (les valeurs de métriques sont envoyées [push] toutes les minutes)|
|Count|real|Généralement égale à 2 (chaque MSEE envoie [push] une valeur métrique unique toutes les minutes)|
|Minimum|real|Minimum des deux valeurs de métriques envoyées (push) par les deux MSEE|
|Maximale|real|Maximum des deux valeurs de métriques envoyées (push) par les deux MSEE|
|Average|real|Égale à (Minimum + Maximum)/2|
|Total|real|Somme des deux valeurs de métriques des deux MSEE (principale valeur sur laquelle se concentrer pour la métrique interrogée)|
  
## <a name="next-steps"></a>Étapes suivantes

Configurez votre connexion ExpressRoute.
  
* [Créer et modifier un circuit](expressroute-howto-circuit-arm.md)
* [Créer et modifier une configuration de peering](expressroute-howto-routing-arm.md)
* [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
