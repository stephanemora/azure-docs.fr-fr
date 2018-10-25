---
title: Supervision, métriques et alertes ExpressRoute Azure | Microsoft Docs
description: Cette page fournit des informations sur la supervision ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: fcdaf10ad30cee0e7b79304ff21d67560522875c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377795"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Supervision, métriques et alertes ExpressRoute

 Cet article vous aide à comprendre la supervision, les métriques et les alertes ExpressRoute. Azure Monitor est un emplacement centralisé pour toutes les métriques, alertes et journaux de diagnostic dans Azure.

## <a name="circuit-metrics"></a>Métriques de circuit

Pour accéder aux **Métriques**, cliquez sur la page ExpressRoute du circuit que vous souhaitez surveiller. Sous **Supervision**, vous pouvez visualiser les **Métriques**. Sélectionnez BitsInPerSecond ou BitsOutPerSecond, et Aggregation. Si vous le souhaitez, vous pouvez appliquer le fractionnement, qui affiche les métriques par type d’appairage.

![métriques de circuit](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Métriques par homologation

Vous pouvez afficher des mesures pour l’homologation privée, publique et Microsoft en bits par seconde.

![métriques par homologation](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

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
* Configurez votre connexion ExpressRoute.
  
  * [Créer et modifier un circuit](expressroute-howto-circuit-arm.md)
  * [Créer et modifier une configuration de l’homologation](expressroute-howto-routing-arm.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
