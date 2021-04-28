---
title: 'Azure ExpressRoute : Supervision, métriques et alertes'
description: Découvrez la surveillance, les métriques et les alertes Azure ExpressRoute à l’aide d’Azure Monitor, le guichet unique pour l’ensemble des métriques, alertes et journaux de diagnostic dans Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725779"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Supervision, métriques et alertes ExpressRoute

Cet article vous aide à comprendre la supervision, les métriques et les alertes ExpressRoute à l’aide d’Azure Monitor. Azure Monitor est un emplacement centralisé pour toutes les métriques, alertes et journaux de diagnostic dans Azure.
 
>[!NOTE]
>L’utilisation des **métriques classiques** n’est pas recommandée.
>

## <a name="expressroute-metrics"></a>Métriques ExpressRoute

Pour afficher les **métriques**, accédez à la page *Azure Monitor*, puis sélectionnez *Métriques*. Pour voir les métriques **ExpressRoute**, filtrez les *circuits ExpressRoute* par type de ressource. Pour afficher les métriques **Global Reach**, filtrez les *circuits ExpressRoute* par type de ressource, puis sélectionnez une ressource de circuit ExpressRoute où Global Reach est activé. Pour afficher les métriques **ExpressRoute Direct**, filtrez les *ports ExpressRoute* par type de ressource. 

Une fois qu’une métrique est sélectionnée, l’agrégation par défaut est appliquée. Si vous le souhaitez, vous pouvez appliquer une division pour afficher les métriques avec différentes dimensions.

### <a name="aggregation-types"></a>Types d’agrégation :

Metrics Explorer prend en charge les [types d’agrégation](../azure-monitor/essentials/metrics-charts.md#aggregation) SUM, MAX, MIN, AVG et COUNT. Vous devez utiliser le type d’agrégation recommandé lorsque vous examinez les insights pour chaque métrique ExpressRoute.

* Sum : Somme de toutes les valeurs capturées pendant l’intervalle d’agrégation. 
* Nombre : Nombre de mesures capturées pendant l’intervalle d’agrégation. 
* Average : Moyenne des valeurs métriques capturées pendant l’intervalle d’agrégation. 
* Min : Plus petite valeur capturée pendant l’intervalle d’agrégation. 
* Max : Plus grande valeur capturée pendant l’intervalle d’agrégation. 

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

Type d’agrégation : *Avg*

Vous pouvez voir les métriques sur l’ensemble des peerings sur un circuit ExpressRoute donné.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="métriques de circuit":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits entrants et sortants : métriques par peering

Type d’agrégation : *Avg*

Vous pouvez afficher des mesures pour le peering privé, public et Microsoft en bits par seconde.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="métriques par peering":::

### <a name="bgp-availability---split-by-peer"></a>Disponibilité du protocole BGP : découpage par pair  

Type d’agrégation : *Avg*

Vous pouvez consulter la disponibilité du protocole BGP (connectivité de couche 3) en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord montre que l’état de la session BGP principale est « Actif » pour le peering privé, et que l’état de la session BGP secondaire est « Inactif » pour le peering privé. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilité du protocole BGP par pair":::

### <a name="arp-availability---split-by-peering"></a>Disponibilité du protocole ARP : découpage par peering  

Type d’agrégation : *Avg*

Vous pouvez consulter la disponibilité du protocole [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (connectivité de couche 3) en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord montre que l’état de la session ARP de peering privé est « Actif » pour les deux pairs, mais « Inactif » pour le peering Microsoft pour les deux pairs. L’agrégation par défaut (moyenne) a été utilisée sur les deux pairs.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilité du protocole ARP par pair":::

## <a name="expressroute-direct-metrics"></a>Métriques de ExpressRoute Direct

### <a name="admin-state---split-by-link"></a>État d’administration – Lien Diviser par

Type d’agrégation : *Avg*

Vous pouvez afficher l’état d’administration pour chaque liaison de la paire de ports ExpressRoute Direct. L’état d’administration indique si le port physique est activé ou désactivé. Cet état est nécessaire afin de transmettre le trafic par le biais de la connexion ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="État de l'administrateur ER Direct":::

### <a name="bits-in-per-second---split-by-link"></a>Bits entrants par seconde – Lien Diviser par

Type d’agrégation : *Avg*

Vous pouvez afficher les bits entrants par seconde sur les deux liens de la paire de ports ExpressRoute Direct. Supervisez ce tableau de bord afin de comparer la bande passante entrante pour les deux liaisons.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Bits ER Direct entrants par seconde":::

### <a name="bits-out-per-second---split-by-link"></a>Bits sortants par seconde – Lien Diviser par

Type d’agrégation : *Avg*

Vous pouvez également afficher les bits sortants par seconde sur les deux liens de la paire de ports ExpressRoute Direct. Supervisez ce tableau de bord afin de comparer la bande passante sortante pour les deux liaisons.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Bits ER Direct sortants par seconde":::

### <a name="line-protocol---split-by-link"></a>Protocole de ligne – Lien Diviser par

Type d’agrégation : *Avg*

Vous pouvez afficher le protocole de ligne sur chaque lien de la paire de ports ExpressRoute Direct. Le protocole de ligne indique si la liaison physique est opérationnelle sur ExpressRoute Direct. Supervisez ce tableau de bord et définissez des alertes afin d’être informé en cas de perte de la connexion physique.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocole de ligne ER Direct":::

### <a name="rx-light-level---split-by-link"></a>Niveau d’éclairage de réception – Lien Diviser par

Type d’agrégation : *Avg*

Vous pouvez afficher le niveau d’éclairage de réception (niveau d’éclairage que le port ExpressRoute Direct **reçoit**) pour chaque port. Les niveaux d’éclairage de réception sains s’inscrivent généralement dans une plage comprise entre -10 dBm et 0 dBm. Définissez des alertes pour être averti si le niveau d’éclairage de réception se situe en dehors de la plage saine.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Niveau d'éclairage de réception de ligne ER Direct":::

### <a name="tx-light-level---split-by-link"></a>Niveau d’éclairage de transmission – Lien Diviser par

Type d’agrégation : *Avg*

Vous pouvez afficher le niveau d’éclairage de transmission (niveau d’éclairage que le port ExpressRoute Direct **transmet**) pour chaque port. Les niveaux d’éclairage de transmission sains s’inscrivent généralement dans une plage comprise entre -10 dBm et 0 dBm. Définissez des alertes pour être averti si le niveau d’éclairage de transmission se situe en dehors de la plage saine.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Niveau d'éclairage de transmission de ligne ER Direct":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Métriques de passerelle de réseau virtuel ExpressRoute

Type d’agrégation : *Avg*

Lorsque vous déployez une passerelle ExpressRoute, Azure gère le calcul et les fonctions de votre passerelle. Six métriques de passerelle sont à votre disposition pour vous aider à mieux comprendre les performances de votre passerelle :

* Utilisation du processeur
* Paquets par seconde
* Nombre de routes publiées pour le pair
* Nombre de routes apprises du pair
* Fréquence des routes modifiées
* Nombre de machines virtuelles dans le réseau virtuel  

Nous vous recommandons vivement de définir des alertes pour chacune de ces métriques, afin d’être conscient des éventuels problèmes de performances de votre passerelle.

### <a name="cpu-utilization---split-instance"></a>Utilisation du processeur - Instance fractionnée

Type d’agrégation : *Avg*

Vous pouvez consulter l’utilisation du processeur de chaque instance de passerelle. L’utilisation du processeur peut présenter un bref pic pendant la maintenance de l’hôte de routine, mais une utilisation élevée et prolongée du processeur peut indiquer que votre passerelle atteint un goulot d’étranglement au niveau des performances. L’augmentation de la taille de la passerelle ExpressRoute peut résoudre ce problème. Définissez une alerte portant sur la fréquence à laquelle l’utilisation du processeur dépasse un certain seuil.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Capture d’écran de l’utilisation du processeur - métriques fractionnées.":::

### <a name="packets-per-second---split-by-instance"></a>Paquets par seconde - Fractionner par instance

Type d’agrégation : *Avg*

Cette métrique capture le nombre de paquets entrants traversant la passerelle ExpressRoute. Vous devriez voir un flux de données constant ici si votre passerelle reçoit du trafic à partir de votre réseau local. Définissez une alerte afin de signaler quand le nombre de paquets par seconde descend en dessous d’un seuil indiquant que votre passerelle ne reçoit plus de trafic.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Capture d’écran des paquets par seconde - métriques fractionnées":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Nombre d’itinéraires publiés pour l’homologue – Fractionné par instance

Type d’agrégation : *Count*

Cette métrique correspond au nombre de routes que la passerelle ExpressRoute publie sur le circuit. Les espaces d’adressage peuvent inclure les réseaux virtuels connectés à l’aide du peering de VNet et utilisant la passerelle ExpressRoute distante. Le nombre de routes devrait rester constant, sauf en cas de modifications fréquentes des espaces d’adressage de réseau virtuel. Définissez une alerte afin d’être averti lorsque le nombre de routes publiées chute au-dessous du seuil du nombre d’espaces d’adressage de réseau virtuel dont vous avez connaissance.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Capture d’écran du nombre de routes publiées pour le pair.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Nombre d’itinéraires appris de l’homologue – Fractionné par instance

Type d’agrégation : *Max*

Cette métrique indique le nombre de routes que la passerelle ExpressRoute apprend à partir de pairs connectés au circuit ExpressRoute. Ces routes peuvent provenir d’un autre réseau virtuel connecté au même circuit, ou être apprises localement. Définissez une alerte afin d’être averti lorsque le nombre de routes apprises chute au-dessous d’un certain seuil. Cela peut indiquer que la passerelle rencontre un problème de performances, ou que des paires distants ne publient plus de routes vers le circuit ExpressRoute. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Capture d’écran du nombre de routes apprises des pairs.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Fréquence de changement d’itinéraire – Fractionnée par instance

Type d’agrégation : *Sum*

Cette métrique indique la fréquence à laquelle les routes sont apprises ou publiées vers les pairs distants. Vous devez d’abord examiner vos appareils locaux afin de comprendre pourquoi le réseau change si fréquemment. Une fréquence élevée de changement de route peut indiquer un problème de performances au niveau de la passerelle ExpressRoute qui pourrait être résolu par la mise à l’échelle du SKU de la passerelle. Définissez une alerte de seuil de fréquence afin d’être averti lorsque votre passerelle ExpressRoute présente des changements de routes anormaux.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Capture d’écran de la métrique de fréquence de changements de routes.":::

### <a name="number-of-vms-in-the-virtual-network"></a>Nombre de machines virtuelles dans le réseau virtuel

Type d’agrégation : *Max*

Cette métrique indique le nombre de machines virtuelles qui utilisent la passerelle ExpressRoute. La quantité de machines virtuelles peut inclure des machines virtuelles de réseaux virtuels appairés qui utilisent la même passerelle ExpressRoute. Définissez une alerte pour cette métrique afin d’être averti si le nombre de machines virtuelles dépasse un certain seuil susceptible d’affecter les performances de la passerelle. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Capture d’écran de la métrique du nombre de machines virtuelles dans le réseau virtuel.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connexions de passerelle ExpressRoute en bits/secondes

Type d’agrégation : *Avg*

Cette métrique indique l’utilisation de la bande passante pour une connexion spécifique à un circuit ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Capture d’écran de la métrique d’utilisation de la bande passante de connexion de passerelle.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertes pour les connexions de passerelle ExpressRoute

1. Pour configurer des alertes, accédez à **Azure Monitor**, puis sélectionnez **Alertes**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Sélectionnez **+Sélectionner la cible** et sélectionnez la ressource de connexion de passerelle ExpressRoute.

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

## <a name="more-metrics-in-log-analytics"></a>Autres métriques dans Log Analytics

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
