---
title: Supervision, métriques et alertes - Azure ExpressRoute | Microsoft Docs
description: Cette page fournit des informations sur la supervision ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991474"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Supervision, métriques et alertes ExpressRoute

Cet article vous aide à comprendre la supervision, les métriques et les alertes ExpressRoute à l’aide d’Azure Monitor. Azure Monitor est un emplacement centralisé pour toutes les métriques, alertes et journaux de diagnostic dans Azure.
 
>[!NOTE]
>L’utilisation des **métriques classiques** n’est pas recommandée.
>

## <a name="circuit-metrics"></a>Métriques de circuit

Pour accéder aux **Métriques**, cliquez sur la page ExpressRoute du circuit que vous souhaitez surveiller. Sous **Supervision**, vous pouvez voir les **Métriques**. Effectuez votre sélection parmi les métriques listées ci-après. L’agrégation par défaut s’applique. Si vous le souhaitez, vous pouvez appliquer un fractionnement, qui présente les métriques avec différentes dimensions.

### <a name="metrics-available"></a>Métriques disponibles : 
* **Disponibilité** 
    * Disponibilité du protocole ARP
      * Dimensions disponibles :
        * Pair (routeur ExpressRoute principal/secondaire)
        * Type de peering (privé/public/Microsoft)
    * Disponibilité du protocole BGP
      * Dimensions disponibles :
        * Pair (routeur ExpressRoute principal/secondaire)
        * Type de peering (privé/public/Microsoft)
* **Trafic**
    * BitsInPerSecond
      * Dimensions disponibles :
        * Type de peering (privé/public/Microsoft)
    * BitsOutPerSecond
      * Dimensions disponibles :
        * Type de peering (privé/public/Microsoft)
    * GlobalReachBitsInPerSecond
      * Dimensions disponibles :
        * Skey de circuit appairé (clé de service)
    * GlobalReachBitsOutPerSecond
      * Dimensions disponibles :
        * Skey de circuit appairé (clé de service)

>[!NOTE]
>L’utilisation des métriques *GlobalGlobalReachBitsInPerSecond* et *GlobalGlobalReachBitsOutPerSecond* sera visible uniquement si au moins une connexion Global Reach est établie.
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits entrants et sortants : métriques sur l’ensemble des peerings

Vous pouvez voir les métriques sur l’ensemble des peerings sur un circuit ExpressRoute donné.

![métriques de circuit](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>Bits entrants et sortants : métriques par peering

Vous pouvez afficher des mesures pour l’homologation privée, publique et Microsoft en bits par seconde.

![métriques par homologation](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>Disponibilité du protocole BGP : découpage par pair  

Vous pouvez consulter la disponibilité du protocole BGP en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord présente la session BGP principale en haut pour le peering privé et la session BGP secondaire en bas pour le peering privé. 

![Disponibilité du protocole BGP par pair](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>Disponibilité du protocole ARP : découpage par peering  

Vous pouvez consulter la disponibilité du protocole [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord présente la session ARP de peering privé en haut sur les deux pairs, mais la session complète en bas pour le peering Microsoft sur les peerings. L’agrégation par défaut (moyenne) a été utilisée sur les deux pairs.  

![Disponibilité du protocole ARP par pair](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connexions de passerelle ExpressRoute en bits/secondes

![connexions de passerelle](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertes pour les connexions de passerelle ExpressRoute

1. Pour configurer des alertes, accédez à **Azure Monitor**, puis cliquez sur **Alertes**.

   ![alertes](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Cliquez sur **+Sélectionner la cible** et sélectionnez la ressource de connexion de passerelle ExpressRoute.

   ![cible]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Définissez les détails de l’alerte.

   ![groupe d’actions](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Définissez et ajoutez le groupe d’actions.

   ![ajouter un groupe d'actions](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertes basées sur chaque homologation

 ![quoi](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurer des alertes pour les journaux d’activité sur des circuits

Dans les **Critères d’alerte**, vous pouvez sélectionner le Type de signal **Journal d’activité** puis sélectionner le Signal.

  ![autre](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Étapes suivantes

Configurez votre connexion ExpressRoute.
  
  * [Créer et modifier un circuit](expressroute-howto-circuit-arm.md)
  * [Créer et modifier une configuration de l’homologation](expressroute-howto-routing-arm.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
