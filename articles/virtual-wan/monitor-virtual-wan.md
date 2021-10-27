---
title: Supervision d’Azure Virtual WAN
description: En savoir plus sur les journaux et métriques Azure Virtual WAN à l’aide d’Azure Monitor.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: fd0eeb94bb35248aababa3f3a8d9fe820d4abe7f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002868"
---
# <a name="monitoring-virtual-wan"></a>Supervision de Virtual WAN

Vous pouvez surveiller Azure Virtual WAN à l’aide d’Azure Monitor. Virtual WAN est un service de mise en réseau qui combine un grand nombre de fonctionnalités de mise en réseau, de sécurité et de routage pour fournir une interface opérationnelle unique. Les passerelles VPN Virtual WAN, les passerelles ExpressRoute et Pare-feu Azure disposent de journaux et de métriques disponibles via Azure Monitor. 

Cet article présente les métriques et les diagnostics disponibles via le portail. Les métriques sont légères et peuvent prendre en charge des scénarios en quasi-temps réel, ce qui les rend utiles pour les alertes et la détection rapide de problèmes.

### <a name="monitoring-secured-hub-azure-firewall"></a>Surveillance du hub sécurisé (Pare-feu Azure) 

Si vous avez choisi de sécuriser votre hub virtuel à l'aide du Pare-feu Azure, les journaux et métriques pertinents sont disponibles ici : [Journaux et métriques du Pare-feu Azure](../firewall/logs-and-metrics.md).
Vous pouvez superviser le hub sécurisé à l’aide des journaux et métriques du Pare-feu Azure. Vous pouvez également utiliser les journaux d’activité pour auditer les opérations sur les ressources de Pare-feu Azure.
Pour chaque réseau Azure Virtual WAN que vous sécurisez et convertissez en hub sécurisé, un objet de ressource de pare-feu explicite est créé dans le groupe de ressources où se trouve le hub. 

:::image type="content" source="./media/monitor-virtual-wan/firewall-resources-portal.png" alt-text="Capture d’écran montrant une ressource de pare-feu dans le groupe de ressources du hub vWAN.":::

La configuration des diagnostics et de la journalisation doit être effectuée à partir de lui en accédant à l’onglet **Paramètre de diagnostic** :

:::image type="content" source="./media/monitor-virtual-wan/firewall-diagnostic-settings.png" alt-text="Capture d’écran montrant les paramètres de diagnostic du pare-feu.":::


## <a name="metrics"></a>Mesures

Les métriques dans Azure Monitor sont des valeurs numériques décrivant certains aspects d’un système à un moment donné. Les métriques sont collectées toutes les minutes et sont utiles pour les alertes, car elles peuvent être échantillonnées fréquemment. Une alerte peut être déclenchée rapidement avec une logique relativement simple.

### <a name="site-to-site-vpn-gateways"></a>Passerelles VPN site à site

Les métriques suivantes sont disponibles pour les passerelles VPN site à site Azure :

#### <a name="tunnel-packet-drop-metrics"></a>Métriques de dépôt de paquets Tunnel
| Métrique | Description|
| --- | --- |
| **Nombre d’annulations de paquets de sortie par tunnel** | Nombre de paquets sortants annulés par tunnel.|
| **Nombre d’annulations de paquets d’entrée par tunnel** | Nombre de paquets entrants annulés par tunnel.|
| **Rejets de paquets NAT de tunnel** | Nombre de paquets traduits rejetés sur un tunnel, par type de rejet et règle NAT.|
| **Rejet de paquets TS de sortie de tunnel pour incompatibilité** | Nombre de rejets de paquets sortants du sélecteur de trafic pour incompatibilité dans un tunnel.|
| **Rejet de paquets TS d’entrée de tunnel pour incompatibilité** | Nombre de rejets de paquets entrants du sélecteur de trafic pour incompatibilité dans un tunnel.|

#### <a name="ipsec-metrics"></a>Métriques IPSEC
| Métrique | Description|
| --- | --- |
| **Nombre de tunnels MMSA** | Nombre de MMSA créés ou supprimés.|
| **Nombre de tunnels QMSA** | Nombre de QMSA IPSEC créés ou supprimés.|

#### <a name="routing-metrics"></a>Métriques de routage
| Métrique | Description|
| --- | --- |
| **État du pair BGP** | État de connectivité BGP par homologue et par instance.|
| **Itinéraires BGP publiés** | Nombre de routes publiées par pair et par instance.|
| **Itinéraires BGP appris** | Nombre de routes apprises par pair et par instance.|
| **Nombre de préfixes d’adresse de réseau virtuel** | Nombre de préfixes d’adresse de réseau virtuel qui sont utilisés/publiés par la passerelle.|

Vous pouvez consulter les métriques par pair et par instance en sélectionnant **Appliquer la division** et en choisissant la valeur par défaut. 

#### <a name="traffic-flow-metrics"></a>Métrique de flux de circulation
| Métrique | Description|
| --- | --- |
| **Bande passante de passerelle** | Bande passante moyenne agrégée site à site d’une passerelle en octets par seconde.|
| **Bande passante de tunnel** | Bande passante moyenne d’un tunnel en octets par seconde.|
| **Octets de sortie de tunnel** | Octets sortants d’un tunnel. |
| **Paquets de sortie de tunnel** | Nombre de paquets sortants d’un tunnel. |
| **Octets d’entrée de tunnel** | Octets entrants d’un tunnel.|
| **Paquets en entrée du tunnel** | Nombre de paquets entrants d’un tunnel.|
| **Pic PPS dans le tunnel** | Nombre de paquets par seconde par connexion de liaison au cours de la dernière minute.|
| **Nombre de flux Tunnel** | Nombre de flux distincts créés par connexion de liaison.|

### <a name="point-to-site-vpn-gateways"></a>Passerelles VPN point à site

Les métriques suivantes sont disponibles pour les passerelles VPN point à site Azure :

| Métrique | Description|
| --- | --- |
| **Bande passante P2S de passerelle** | Bande passante moyenne agrégée point à site d’une passerelle en octets par seconde. |
| **Nombre de connexions P2S** |Nombre de connexions point à site d’une passerelle. Nombre de connexions point à site d’une passerelle. Pour veiller à consulter les métriques exactes dans Azure Monitor, sélectionnez **Somme** comme **type d’agrégation** pour **Nombre de connexions P2S**. Vous pouvez également sélectionner **Max** si vous fractionnez également par **Instance**. |
| **Nombre d’itinéraires VPN de l’utilisateur** | Nombre d’itinéraires VPN utilisateur configurés sur la passerelle VPN. Cette métrique peut être répartie en itinéraires **statiques** et en itinéraires **dynamiques**.

### <a name="azure-expressroute-gateways"></a>Passerelles ExpressRoute Azure

Les métriques suivantes sont disponibles pour les passerelles ExpressRoute Azure :

| Métrique | Description|
| --- | --- |
| **BitsInPerSecond** | Bits entrant dans Azure par seconde.|
| **BitsOutPerSecond** | Bits sortant d’Azure par seconde. |
| **Utilisation du processeur** | Utilisation du processeur de la passerelle ExpressRoute.|
| **Paquets par seconde** | Nombre de paquets de la passerelle ExpressRoute.|
| **Nombre de routes publiées pour le pair**| Nombre d’itinéraires publiés sur le pair par la passerelle ExpressRoute. | 
| **Nombre de routes apprises du pair**| Nombre d’itinéraires appris à partir du pair par la passerelle ExpressRoute.|
| **Fréquence des routes modifiées** | Fréquence des modifications d’itinéraires dans la passerelle ExpressRoute.|
| **Nombre de machines virtuelles dans le réseau virtuel**| Nombre de machines virtuelles qui utilisent cette passerelle ExpressRoute.|

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Afficher les métriques de passerelle

Les étapes suivantes vous aident à localiser et à afficher les métriques :

1. Dans le portail, accédez au hub virtuel qui contient la passerelle.

2. Sélectionnez **VPN (site à site)** pour localiser une passerelle site à site, **ExpressRoute** pour localiser une passerelle ExpressRoute ou **VPN utilisateur (point à site)** pour localiser une passerelle point à site.

3. Sélectionnez **Métriques**.

   :::image type="content" source="./media/monitor-virtual-wan/view-metrics.png" alt-text="Capture d’écran montrant un volet de VPN site à site dans lequel Afficher dans Azure Monitor est sélectionné.":::

4. Dans la page **Métriques**, vous pouvez afficher les métriques qui vous intéressent.

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="Capture d’écran montrant la page « Métriques » avec les catégories en surbrillance.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Journaux de diagnostic

### <a name="site-to-site-vpn-gateways"></a>Passerelles VPN site à site

Les diagnostics suivants sont disponibles pour les passerelles VPN site à site Azure :

| Métrique | Description|
| --- | --- |
| **Journaux de diagnostic de passerelle** | Diagnostics propres à la passerelle, comme l’intégrité, la configuration, les mises à jour du service et des diagnostics supplémentaires.|
| **Journaux de diagnostic de tunnel** | Il s’agit de journaux relatifs au tunnel IPsec, tels que les événements de connexion et de déconnexion pour un tunnel IPsec site à site, les associations de sécurité négociées, les raisons de la déconnexion et des diagnostics supplémentaires.|
| **Journaux de diagnostic d’itinéraires** | Il s’agit des journaux relatifs aux événements pour les itinéraires statiques, Border Gateway Protocol, les mises à jour des itinéraires et des diagnostics supplémentaires. |
| **Journaux de diagnostics IKE** | Diagnostics spécifiques à IKE pour les connexions IPsec. |

### <a name="point-to-site-vpn-gateways"></a>Passerelles VPN point à site

Les diagnostics suivants sont disponibles pour les passerelles VPN point à site Azure :

| Métrique | Description|
| --- | --- |
| **Journaux de diagnostic de passerelle** | Diagnostics spécifiques à la passerelle, tels que l’intégrité, la configuration, les mises à jour du service et d’autres diagnostics. |
| **Journaux de diagnostics IKE** | Diagnostics spécifiques à IKE pour les connexions IPsec.|
| **Journaux de diagnostics PS2** | Il s’agit des événements de client et de configuration P2S (point à site) du VPN des utilisateurs. Il s’agit notamment de la connexion/déconnexion des clients, de l’allocation d’adresses de clients VPN et d’autres diagnostics.|

### <a name="express-route-gateways"></a>Passerelles Express Route

Les journaux de diagnostic pour les passerelles Express Route dans Azure Virtual WAN ne sont pas pris en charge.

### <a name="view-diagnostic-logs-configuration"></a><a name="diagnostic-steps"></a>Afficher la configuration des journaux de diagnostic

Les étapes suivantes vous permettent de créer, modifier et afficher les paramètres de diagnostic :

1. Dans le portail, accédez à votre ressource Virtual WAN, puis sélectionnez **Hubs** dans le groupe **Connectivité**. 

   :::image type="content" source="./media/monitor-virtual-wan/select-hub.png" alt-text="Capture d’écran montrant la sélection du hub dans le portail vWAN.":::

2. Sous le groupe **Connectivité** à gauche, sélectionnez la passerelle dont vous voulez examiner les diagnostics :

   :::image type="content" source="./media/monitor-virtual-wan/select-hub-gateway.png" alt-text="Capture d’écran montrant la section Connectivité du hub.":::

3. Dans la partie droite de la page, cliquez sur le lien **Afficher dans Azure Monitor** à droite de **Journaux**, puis sélectionnez une option. Vous pouvez choisir d’envoyer à Log Analytics, de diffuser vers un Event Hub ou de simplement archiver dans un compte de stockage.

   :::image type="content" source="./media/monitor-virtual-wan/view-hub-gateway-logs.png" alt-text="Capture d’écran de la sélection de l’option Afficher dans Azure Monitor pour les journaux.":::

4. Dans cette page, vous pouvez créer un paramètre de diagnostic ( **+Ajouter un paramètre de diagnostic**) ou en modifier un existant (**Modifier le paramètre**). Vous pouvez choisir d’envoyer les journaux de diagnostic à Log Analytics (comme indiqué dans l’exemple ci-dessous), de les envoyer en streaming vers un hub d’événements, de les envoyer à une solution tierce ou de les archiver dans un compte de stockage.

    :::image type="content" source="./media/monitor-virtual-wan/select-gateway-settings.png" alt-text="Capture d’écran de la sélection des paramètres des journaux de diagnostic.":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Exemple de requête Log Analytics

Si vous avez choisi d’envoyer les données de diagnostic à un espace de travail Log Analytics, alors vous pouvez utiliser des requêtes de type SQL, comme dans l’exemple ci-dessous, pour examiner les données. Pour plus d’informations, consultez [Langage de requête Log Analytics](/services-hub/health/log_analytics_query_language).

L’exemple suivant contient une requête visant à obtenir des diagnostics d’itinéraire site à site.

`AzureDiagnostics | where Category == "RouteDiagnosticLog"`

Remplacez les valeurs ci-dessous, après **= =** , selon vos besoins en vous reportant aux tableaux figurant dans la section précédente de cet article.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

Pour exécuter la requête, vous devez ouvrir la ressource Log Analytics que vous avez configurée pour recevoir les journaux de diagnostic, puis sélectionner **Journaux** sous l’onglet **Général** dans la partie gauche du volet :

:::image type="content" source="./media/monitor-virtual-wan/log-analytics-query-samples.png" alt-text="Exemples de requêtes Log Analytics.":::

Pour obtenir d’autres exemples de requêtes Log Analytics pour la passerelle VPN Azure, à la fois de site à site et de point à site, vous pouvez visiter la page [Résoudre les problèmes de passerelle VPN Azure à l’aide des journaux de diagnostic](../vpn-gateway/troubleshoot-vpn-with-azure-diagnostics.md). Pour le Pare-feu Azure, un [classeur](../firewall/firewall-workbook.md) est fourni afin de faciliter l’analyse des journaux. En utilisant son interface graphique, il est possible d’examiner les données de diagnostic sans écrire manuellement de requêtes Log Analytics. 

## <a name="activity-logs"></a><a name="activity-logs"></a>Journaux d’activité

Les entrées du **journal d’activité** sont recueillies par défaut et peuvent être affichées dans le portail Azure. Vous pouvez utiliser les journaux d’activité Azure (anciennement *journaux des opérations* et *journaux d’audit*) pour afficher toutes les opérations soumises à votre abonnement Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment superviser les métriques et les journaux de Pare-feu Azure, consultez le [Tutoriel : Surveiller les journaux d’activité de pare-feu Azure](../firewall/firewall-diagnostics.md).
* Pour en savoir plus sur les métriques dans Azure Monitor, consultez [Métriques dans Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
