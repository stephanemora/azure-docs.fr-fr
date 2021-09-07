---
title: Supervision d’Azure ExpressRoute
description: Commencez ici pour découvrir comment superviser Azure ExpressRoute.
services: expressroute
author: duongau
ms.author: duau
ms.service: expressroute
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: 4f07659381c852e35585b5127d97c932a05088c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562621"
---
# <a name="monitoring-azure-expressroute"></a>Supervision d’Azure ExpressRoute

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. 

Cet article décrit les données de supervision générées par Azure ExpressRoute. Azure ExpressRoute utilise [Azure Monitor](../azure-monitor/overview.md). Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure qui l’utilisent, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
 
## <a name="expressroute-insights"></a>Insights ExpressRoute

Dans Azure, certains services disposent d’un tableau de bord de supervision prédéfini spécial dans le portail Azure qui constitue un point de départ pour la supervision de votre service. Ces tableaux de bord spéciaux sont appelés « insights ».

ExpressRoute utilise des insights réseau pour fournir un mappage de topologie détaillé de tous les composants ExpressRoute (peerings, connexions, passerelles) les uns par rapport aux autres. Les insights réseau pour ExpressRoute ont également un tableau de bord de métriques préchargé pour la disponibilité, le débit, les suppressions de paquets et les métriques de passerelle. Pour plus d’informations, consultez [Insight Azure ExpressRoute avec des insights réseau](expressroute-network-insights.md).

## <a name="monitoring-data"></a>Données de surveillance 

Azure ExpressRoute collecte les mêmes types de données de supervision que d’autres ressources Azure, lesquelles sont décrites dans [Données de supervision de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Pour obtenir des informations détaillées sur les métriques et les journaux de métriques créés par Azure ExpressRoute, consultez [Informations de référence sur les données de supervision d’Azure ExpressRoute](monitor-expressroute-reference.md).

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour *Azure ExpressRoute* sont listées dans [Informations de référence sur les données de supervision d’Azure ExpressRoute](monitor-expressroute-reference.md#resource-logs).

> [!IMPORTANT]
> L’activation de ces paramètres nécessite des services Azure supplémentaires (compte de stockage, hub d’événements ou Log Analytics), ce qui peut augmenter vos coûts. Pour calculer un coût estimé, consultez la rubrique [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les métriques d’*Azure ExpressRoute* avec des métriques d’autres services Azure à l’aide de Metrics Explorer en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="Capture d’écran du tableau de bord des métriques pour ExpressRoute.":::

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

* Pour voir les métriques **ExpressRoute**, filtrez les *circuits ExpressRoute* par type de ressource. 
* Pour afficher les métriques **Global Reach**, filtrez les *circuits ExpressRoute* par type de ressource, puis sélectionnez une ressource de circuit ExpressRoute où Global Reach est activé. 
* Pour afficher les métriques **ExpressRoute Direct**, filtrez les *ports ExpressRoute* par type de ressource. 

Une fois qu’une métrique est sélectionnée, l’agrégation par défaut est appliquée. Si vous le souhaitez, vous pouvez appliquer une division pour afficher les métriques avec différentes dimensions.

### <a name="aggregation-types"></a>Types d’agrégation

Metrics Explorer prend en charge les [types d’agrégation](../azure-monitor/essentials/metrics-charts.md#aggregation) SUM, MAX, MIN, AVG et COUNT. Utilisez le type d’agrégation recommandé quand vous passez en revue les insights pour chaque métrique ExpressRoute.

* Sum : Somme de toutes les valeurs capturées pendant l’intervalle d’agrégation. 
* Nombre : Nombre de mesures capturées pendant l’intervalle d’agrégation. 
* Average : Moyenne des valeurs métriques capturées pendant l’intervalle d’agrégation. 
* Min : Plus petite valeur capturée pendant l’intervalle d’agrégation. 
* Max : Plus grande valeur capturée pendant l’intervalle d’agrégation.

>[!NOTE]
>L’utilisation des **métriques classiques** n’est pas recommandée.
>

### <a name="circuits-metrics"></a>Métriques des circuits

#### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits entrants et sortants : métriques sur l’ensemble des peerings

Type d’agrégation : *Avg*

Vous pouvez voir les métriques sur l’ensemble des peerings sur un circuit ExpressRoute donné.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="métriques de circuit":::

#### <a name="bits-in-and-out---metrics-per-peering"></a>Bits entrants et sortants - Métriques par peering

Type d’agrégation : *Avg*

Vous pouvez afficher des mesures pour le peering privé, public et Microsoft en bits par seconde.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="métriques par peering":::

#### <a name="bgp-availability---split-by-peer"></a>Disponibilité du protocole BGP - Diviser par pair  

Type d’agrégation : *Avg*

Vous pouvez consulter la disponibilité du protocole BGP (connectivité de couche 3) en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord montre que l’état de la session BGP principale est « Actif » pour le peering privé, et que l’état de la session BGP secondaire est « Inactif » pour le peering privé. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilité du protocole BGP par pair":::

### <a name="arp-availability---split-by-peering"></a>Disponibilité du protocole ARP - Diviser par peering  

Type d’agrégation : *Avg*

Vous pouvez consulter la disponibilité du protocole [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (connectivité de couche 3) en quasi temps réel sur les peerings et les pairs (routeurs ExpressRoute principal et secondaire). Ce tableau de bord montre que l’état de la session ARP de peering privé est « Actif » pour les deux pairs, mais « Inactif » pour le peering Microsoft pour les deux pairs. L’agrégation par défaut (moyenne) a été utilisée sur les deux pairs.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilité du protocole ARP par pair":::

### <a name="expressroute-direct-metrics"></a>Métriques ExpressRoute Direct

#### <a name="admin-state---split-by-link"></a>État d’administration – Lien Diviser par

Type d’agrégation : *Avg*

Vous pouvez afficher l’état d’administration pour chaque liaison de la paire de ports ExpressRoute Direct. L’état d’administration indique si le port physique est activé ou désactivé. Cet état est nécessaire afin de transmettre le trafic par le biais de la connexion ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="État de l'administrateur ER Direct":::

#### <a name="bits-in-per-second---split-by-link"></a>Bits entrants par seconde - Diviser par lien

Type d’agrégation : *Avg*

Vous pouvez afficher les bits entrants par seconde sur les deux liens de la paire de ports ExpressRoute Direct. Supervisez ce tableau de bord afin de comparer la bande passante entrante pour les deux liaisons.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Bits ER Direct entrants par seconde":::

#### <a name="bits-out-per-second---split-by-link"></a>Bits sortants par seconde - Diviser par lien

Type d’agrégation : *Avg*

Vous pouvez également afficher les bits sortants par seconde sur les deux liens de la paire de ports ExpressRoute Direct. Supervisez ce tableau de bord afin de comparer la bande passante sortante pour les deux liaisons.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Bits ER Direct sortants par seconde":::

#### <a name="line-protocol---split-by-link"></a>Protocole de ligne - Diviser par lien

Type d’agrégation : *Avg*

Vous pouvez afficher le protocole de ligne sur chaque lien de la paire de ports ExpressRoute Direct. Le protocole de ligne indique si la liaison physique est opérationnelle sur ExpressRoute Direct. Supervisez ce tableau de bord et définissez des alertes afin d’être informé en cas de perte de la connexion physique.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocole de ligne ER Direct":::

#### <a name="rx-light-level---split-by-link"></a>Niveau d’éclairage de réception - Diviser par lien

Type d’agrégation : *Avg*

Vous pouvez afficher le niveau d’éclairage de réception (niveau d’éclairage que le port ExpressRoute Direct **reçoit**) pour chaque port. Les niveaux d’éclairage de réception sains s’inscrivent généralement dans une plage comprise entre -10 dBm et 0 dBm. Définissez des alertes pour être averti si le niveau d’éclairage de réception se situe en dehors de la plage saine.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Niveau d'éclairage de réception de ligne ER Direct":::

#### <a name="tx-light-level---split-by-link"></a>Niveau d’éclairage de transmission - Diviser par lien

Type d’agrégation : *Avg*

Vous pouvez afficher le niveau d’éclairage de transmission (niveau d’éclairage que le port ExpressRoute Direct **transmet**) pour chaque port. Les niveaux d’éclairage de transmission sains s’inscrivent généralement dans une plage comprise entre -10 dBm et 0 dBm. Définissez des alertes pour être averti si le niveau d’éclairage de transmission se situe en dehors de la plage saine.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Niveau d'éclairage de transmission de ligne ER Direct":::

### <a name="expressroute-virtual-network-gateway-metrics"></a>Métriques de passerelle de réseau virtuel ExpressRoute

Type d’agrégation : *Avg*

Lorsque vous déployez une passerelle ExpressRoute, Azure gère le calcul et les fonctions de votre passerelle. Six métriques de passerelle sont à votre disposition pour vous aider à mieux comprendre les performances de votre passerelle :

* Utilisation du processeur
* Paquets par seconde
* Nombre de routes publiées pour le pair
* Nombre de routes apprises du pair
* Fréquence des routes modifiées
* Nombre de machines virtuelles dans le réseau virtuel  

Nous vous recommandons vivement de définir des alertes pour chacune de ces métriques afin de savoir quand votre passerelle pourrait éventuellement voir des problèmes de performances.

#### <a name="cpu-utilization---split-instance"></a>Utilisation du processeur - Diviser par instance

Type d’agrégation : *Avg*

Vous pouvez consulter l’utilisation du processeur de chaque instance de passerelle. L’utilisation du processeur peut présenter un bref pic pendant la maintenance de l’hôte de routine, mais une utilisation élevée et prolongée du processeur peut indiquer que votre passerelle atteint un goulot d’étranglement au niveau des performances. L’augmentation de la taille de la passerelle ExpressRoute peut résoudre ce problème. Définissez une alerte portant sur la fréquence à laquelle l’utilisation du processeur dépasse un certain seuil.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Capture d’écran de l’utilisation du processeur - métriques fractionnées.":::

#### <a name="packets-per-second---split-by-instance"></a>Paquets par seconde - Diviser par instance

Type d’agrégation : *Avg*

Cette métrique capture le nombre de paquets entrants traversant la passerelle ExpressRoute. Vous devriez voir un flux de données constant ici si votre passerelle reçoit du trafic à partir de votre réseau local. Définissez une alerte afin de signaler quand le nombre de paquets par seconde descend en dessous d’un seuil indiquant que votre passerelle ne reçoit plus de trafic.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Capture d’écran des paquets par seconde - métriques fractionnées":::

#### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Nombre de routes publiées sur le pair - Diviser par instance

Type d’agrégation : *Count*

Cette métrique correspond au nombre de routes que la passerelle ExpressRoute publie sur le circuit. Les espaces d’adressage peuvent inclure les réseaux virtuels connectés à l’aide du peering de VNet et utilisant la passerelle ExpressRoute distante. Le nombre de routes devrait rester constant, sauf en cas de modifications fréquentes des espaces d’adressage de réseau virtuel. Définissez une alerte afin d’être averti lorsque le nombre de routes publiées chute au-dessous du seuil du nombre d’espaces d’adressage de réseau virtuel dont vous avez connaissance.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Capture d’écran du nombre de routes publiées pour le pair.":::

#### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Nombre de routes découvertes à partir du pair - Diviser par instance

Type d’agrégation : *Max*

Cette métrique indique le nombre de routes que la passerelle ExpressRoute apprend à partir de pairs connectés au circuit ExpressRoute. Ces routes peuvent provenir d’un autre réseau virtuel connecté au même circuit, ou être apprises localement. Définissez une alerte afin d’être averti lorsque le nombre de routes apprises chute au-dessous d’un certain seuil. Un nombre élevé de routes supprimées peut indiquer que la passerelle rencontre un problème de performances, ou que des paires distants ne publient plus de routes vers le circuit ExpressRoute. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Capture d’écran du nombre de routes apprises des pairs.":::

#### <a name="frequency-of-routes-change---split-by-instance"></a>Fréquence de changement de routes - Diviser par instance

Type d’agrégation : *Sum*

Cette métrique indique la fréquence à laquelle les routes sont apprises ou publiées vers les pairs distants. Vous devez d’abord examiner vos appareils locaux afin de comprendre pourquoi le réseau change si fréquemment. Une fréquence élevée de changement de route peut indiquer un problème de performances au niveau de la passerelle ExpressRoute qui pourrait être résolu par la mise à l’échelle du SKU de la passerelle. Définissez une alerte de seuil de fréquence afin d’être averti lorsque votre passerelle ExpressRoute présente des changements de routes anormaux.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Capture d’écran de la métrique de fréquence de changements de routes.":::

#### <a name="number-of-vms-in-the-virtual-network"></a>Nombre de machines virtuelles dans le réseau virtuel

Type d’agrégation : *Max*

Cette métrique indique le nombre de machines virtuelles qui utilisent la passerelle ExpressRoute. La quantité de machines virtuelles peut inclure des machines virtuelles de réseaux virtuels appairés qui utilisent la même passerelle ExpressRoute. Définissez une alerte pour cette métrique afin d’être averti si le nombre de machines virtuelles dépasse un certain seuil susceptible d’affecter les performances de la passerelle. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Capture d’écran de la métrique du nombre de machines virtuelles dans le réseau virtuel.":::

#### <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connexions de passerelle ExpressRoute en bits/secondes

Type d’agrégation : *Avg*

Cette métrique indique l’utilisation de la bande passante pour une connexion spécifique à un circuit ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Capture d’écran de la métrique d’utilisation de la bande passante de connexion de passerelle.":::

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Le schéma des journaux de ressources ExpressRoute se trouve dans les [Informations de référence sur les données Azure ExpressRoute](monitor-expressroute-reference.md#schemas). 

Le [journal d’activité](../azure-monitor/essentials/activity-log.md) est une journalisation de plateforme qui fournit des insights sur les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.

ExpressRoute stocke les données dans les tables suivantes.

| Table de charge de travail | Description |
| ----- | ----------- |
| AzureDiagnostics | Table commune utilisée par plusieurs services pour stocker les journaux de ressources. Les journaux de ressources d’ExpressRoute peuvent être identifiés avec `MICROSOFT.NETWORK`. |
| AzureMetrics | Données de métriques émises par ExpressRoute et qui mesurent leur intégrité et leurs performances. 

Pour afficher ces tables, accédez à votre ressource de circuit ExpressRoute, sélectionnez **Journaux** sous *Supervision*.

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

Voici quelques requêtes que vous pouvez entrer dans la barre Recherche dans les journaux pour vous permettre de superviser plus facilement vos ressources Azure ExpressRoute. Ces requêtes fonctionnent avec le [nouveau langage](../azure-monitor/logs/log-query-overview.md).

* Pour interroger une table de route BGP découverte au cours des 12 dernières heures.

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceType , network_s, path_s, OperationName
    ```

* Pour interroger les messages d’information BGP par niveau, par type de ressource et par réseau.

    ```Kusto
    AzureDiagnostics
    | where Level == "Informational"
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceId , Level, ResourceType , network_s, path_s
    ```

* Pour interroger le graphe de trafic BitInPerSeconds au cours de la dernière heure.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsInPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* Pour interroger le graphe de trafic BitOutPerSeconds au cours de la dernière heure.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsOutPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* Pour interroger le graphe ArpAvailability par intervalles de 5 minutes.
    
    ```Kusto
    AzureMetrics
    | where MetricName == "ArpAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

* Pour interroger le graphe de disponibilité du protocole BGP par intervalles de 5 minutes.

    ```Kusto
    AzureMetrics
    | where MetricName == "BGPAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients.

Le tableau suivant liste les règles d’alerte courantes et recommandées pour ExpressRoute.

| Type d’alerte | Condition | Description  |
|:---|:---|:---|
| Inactivité de la disponibilité du protocole ARP | Nom de la dimension : Type de peering, Type d’agrégation : Avg, Opérateur : Inférieur à, Valeur de seuil : 100 % | Quand la disponibilité du protocole ARP est inactive pour un type de peering. |
| Inactivité de la disponibilité du protocole BGP | Nom de la dimension : Pair, Type d’agrégation : Avg, Opérateur : Inférieur à, Valeur de seuil : 100 % | Quand la disponibilité du protocole BGP est inactive pour un pair. |

### <a name="alerts-for-expressroute-gateway-connections"></a>Alertes pour les connexions de passerelle ExpressRoute

1. Pour configurer des alertes, accédez à **Azure Monitor**, puis sélectionnez **Alertes**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Sélectionnez **+ Sélectionner une cible**, puis sélectionnez la ressource de connexion de passerelle ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="cible":::
3. Définissez les détails de l’alerte.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="groupe d’actions":::
4. Définissez et ajoutez le groupe d’actions.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="ajouter un groupe d'actions":::


### <a name="alerts-based-on-each-peering"></a>Alertes basées sur chaque peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="chaque peering":::

### <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurer des alertes pour les journaux d’activité sur des circuits

Dans les **Critères d’alerte**, vous pouvez sélectionner le Type de signal **Journal d’activité** puis sélectionner le Signal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="journaux d'activité":::

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des informations de référence sur les métriques, les journaux et d’autres valeurs importantes créées par ExpressRoute, consultez [Informations de référence sur la supervision des données ExpressRoute](monitor-expressroute-reference.md).
* Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/overview.md).
