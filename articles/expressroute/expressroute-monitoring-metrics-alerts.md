---
title: 'Azure ExpressRoute : Supervision, métriques et alertes'
description: Découvrez la surveillance, les métriques et les alertes Azure ExpressRoute à l’aide d’Azure Monitor, le guichet unique pour l’ensemble des métriques, alertes et journaux de diagnostic dans Azure.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 49e5acb7fc0cfe947d846f2943fb5071d6554ea5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192465"
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
|BitsInPerSecond|Trafic|<ui><li> Type d’appairage (ExpressRoute)</ui></li><ui><li>Liaison (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trafic| <ui><li>Type d’appairage (ExpressRoute)</ui></li><ui><li> Liaison (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
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

![métriques de circuit](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits entrants et sortants : métriques par peering

Vous pouvez afficher des mesures pour le peering privé, public et Microsoft en bits par seconde.

![métriques par peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilité du protocole BGP : découpage par pair  

Vous pouvez consulter la disponibilité du protocole BGP en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord présente la session BGP principale en haut pour le peering privé et la session BGP secondaire en bas pour le peering privé. 

![Disponibilité du protocole BGP par pair](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilité du protocole ARP : découpage par peering  

Vous pouvez consulter la disponibilité du protocole [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord présente la session ARP de peering privé en haut sur les deux pairs, mais la session complète en bas pour le peering Microsoft sur les peerings. L’agrégation par défaut (moyenne) a été utilisée sur les deux pairs.  

![Disponibilité du protocole ARP par pair](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Métriques de ExpressRoute Direct

### <a name="admin-state---split-by-link"></a>État d’administration – Lien Diviser par
Vous pouvez afficher l’état d’administration pour chaque lien de la paire de ports directs ExpressRoute Direct.

![état d’administration d’expressroute direct](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits entrants par seconde – Lien Diviser par
Vous pouvez afficher les bits entrants par seconde sur les deux liens de la paire de ports ExpressRoute Direct. 

![bits entrants d’expressroute direct par seconde](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits sortants par seconde – Lien Diviser par
Vous pouvez également afficher les bits sortants par seconde sur les deux liens de la paire de ports ExpressRoute Direct. 

![bits sortants d’expressroute direct par seconde](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protocole de ligne – Lien Diviser par
Vous pouvez afficher le protocole de ligne sur chaque lien de la paire de ports ExpressRoute Direct.

![Protocole de ligne d’expressroute direct](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Niveau d’éclairage de réception – Lien Diviser par
Vous pouvez afficher le niveau d’éclairage de réception (niveau d’éclairage que le port ExpressRoute Direct **reçoit**) pour chaque port. Les niveaux d’éclairage de réception sains s’inscrivent généralement dans une plage de -10 à 0 dBm

![Niveau d’éclairage de réception de ligne d’expressroute direct](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Niveau d’éclairage de transmission – Lien Diviser par
Vous pouvez afficher le niveau d’éclairage de transmission (niveau d’éclairage que le port ExpressRoute Direct **transmet**) pour chaque port. Les niveaux d’éclairage de transmission sains s’inscrivent généralement dans une plage de -10 à 0 dBm

![Niveau d’éclairage de réception de ligne d’expressroute direct](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connexions de passerelle ExpressRoute en bits/secondes

![connexions de passerelle](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertes pour les connexions de passerelle ExpressRoute

1. Pour configurer des alertes, accédez à **Azure Monitor**, puis cliquez sur **Alertes**.

   ![alertes](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Cliquez sur **+Sélectionner la cible** et sélectionnez la ressource de connexion de passerelle ExpressRoute.

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Définissez les détails de l’alerte.

   ![groupe d’actions](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Définissez et ajoutez le groupe d’actions.

   ![ajouter un groupe d'actions](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertes basées sur chaque peering

 ![quoi](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurer des alertes pour les journaux d’activité sur des circuits

Dans les **Critères d’alerte**, vous pouvez sélectionner le Type de signal **Journal d’activité** puis sélectionner le Signal.

  ![autre](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Étapes suivantes

Configurez votre connexion ExpressRoute.
  
  * [Créer et modifier un circuit](expressroute-howto-circuit-arm.md)
  * [Créer et modifier une configuration de peering](expressroute-howto-routing-arm.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
