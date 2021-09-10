---
title: Supervision d’Azure Virtual WAN
description: En savoir plus sur les journaux et métriques Azure Virtual WAN à l’aide d’Azure Monitor.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 25e12ce4fd361cb053eae8b0d9992031a91d4616
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469013"
---
# <a name="monitoring-virtual-wan"></a>Supervision de Virtual WAN

Vous pouvez surveiller Azure Virtual WAN à l’aide d’Azure Monitor. Virtual WAN est un service de mise en réseau qui combine un grand nombre de fonctionnalités de mise en réseau, de sécurité et de routage pour fournir une interface opérationnelle unique. Les passerelles VPN Virtual WAN, les passerelles ExpressRoute et Pare-feu Azure disposent de journaux et de métriques disponibles via Azure Monitor.

Cet article présente les métriques et les diagnostics disponibles via le portail. Les métriques sont légères et peuvent prendre en charge des scénarios en quasi-temps réel, ce qui les rend utiles pour les alertes et la détection rapide de problèmes.

### <a name="monitoring-secured-hub-azure-firewall"></a>Surveillance du hub sécurisé (Pare-feu Azure) 

Vous pouvez surveiller le hub sécurisé à l'aide des journaux du Pare-feu Azure. Vous pouvez également utiliser les journaux d’activité pour auditer les opérations sur les ressources de Pare-feu Azure.

Si vous avez choisi de sécuriser votre hub virtuel à l'aide du Pare-feu Azure, les journaux et métriques pertinents sont disponibles ici : [Journaux et métriques du Pare-feu Azure](../firewall/logs-and-metrics.md).

## <a name="metrics"></a>Mesures

Les métriques dans Azure Monitor sont des valeurs numériques décrivant certains aspects d’un système à un moment donné. Les métriques sont collectées toutes les minutes et sont utiles pour les alertes, car elles peuvent être échantillonnées fréquemment. Une alerte peut être déclenchée rapidement avec une logique relativement simple.

### <a name="site-to-site-vpn-gateways"></a>Passerelles VPN site à site

Les métriques suivantes sont disponibles pour les passerelles VPN site à site Azure :

| Métrique | Description|
| --- | --- |
| **Bande passante de passerelle** | Bande passante moyenne agrégée site à site d’une passerelle en octets par seconde.|
| **Bande passante de tunnel** | Bande passante moyenne d’un tunnel en octets par seconde.|
| **Octets de sortie de tunnel** | Octets sortants d’un tunnel. |
| **Paquets de sortie de tunnel** | Nombre de paquets sortants d’un tunnel. |
| **Rejet de paquets TS de sortie de tunnel pour incompatibilité** | Nombre de rejets de paquets sortants du sélecteur de trafic pour incompatibilité dans un tunnel.|
| **Octets d’entrée de tunnel** | Octets entrants d’un tunnel.|
| **Paquets en entrée du tunnel** | Nombre de paquets entrants d’un tunnel.|
| **Rejet de paquets TS d’entrée de tunnel pour incompatibilité** | Nombre de rejets de paquets entrants du sélecteur de trafic pour incompatibilité dans un tunnel.|

### <a name="point-to-site-vpn-gateways"></a>Passerelles VPN point à site

Les métriques suivantes sont disponibles pour les passerelles VPN point à site Azure :

| Métrique | Description|
| --- | --- |
| **Bande passante P2S de passerelle** | Bande passante moyenne agrégée point à site d’une passerelle en octets par seconde. |
| **Nombre de connexions P2S** |Nombre de connexions point à site d’une passerelle. Nombre de connexions point à site d’une passerelle. Pour vous assurer de voir des métriques exactes dans Azure Monitor, sélectionnez **Somme** comme **type d’agrégation** pour **Nombre de connexions P2S**. Vous pouvez également sélectionner **Max** si vous fractionnez également par **Instance**. |

### <a name="azure-expressroute-gateways"></a>Passerelles ExpressRoute Azure

Les métriques suivantes sont disponibles pour les passerelles ExpressRoute Azure :

| Métrique | Description|
| --- | --- |
| **BitsInPerSecond** | Bits entrant dans Azure par seconde.|
| **BitsOutPerSecond** | Bits sortant d’Azure par seconde. |

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Afficher les métriques de passerelle

Les étapes suivantes vous aident à localiser et à afficher les métriques :

1. Dans le portail, accédez au hub virtuel qui contient la passerelle.

2. Sélectionnez **VPN (site à site)** pour localiser une passerelle site à site, **ExpressRoute** pour localiser une passerelle ExpressRoute ou **VPN utilisateur (point à site)** pour localiser une passerelle point à site. Sur la page, vous pouvez voir les informations de la passerelle. Copiez ces informations. Vous les utiliserez ultérieurement pour afficher les diagnostics à l’aide d’Azure Monitor.

3. Sélectionnez **Métriques**.

   :::image type="content" source="./media/monitor-virtual-wan/metrics.png" alt-text="Capture d'écran d'un volet de VPN site à site dans lequel Afficher dans Azure Monitor est sélectionné.":::

4. Dans la page **Métriques**, vous pouvez afficher les métriques qui vous intéressent.

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="Capture d’écran montrant la page « Métriques » avec les catégories en surbrillance.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Journaux de diagnostic

### <a name="site-to-site-vpn-gateways"></a>Passerelles VPN site à site

Les diagnostics suivants sont disponibles pour les passerelles VPN site à site Azure :

| Métrique | Description|
| --- | --- |
| **Journaux de diagnostic de passerelle** | Diagnostics spécifiques à la passerelle, tels que l’intégrité, la configuration, les mises à jour du service, ainsi que des diagnostics supplémentaires.|
| **Journaux de diagnostic de tunnel** | Il s’agit de journaux relatifs au tunnel IPsec, tels que les événements de connexion et de déconnexion pour un tunnel IPsec site à site, les associations de sécurité négociées, les raisons de la déconnexion, ainsi que des diagnostics supplémentaires.|
| **Journaux de diagnostic d’itinéraires** | Il s’agit des journaux relatifs aux événements pour les itinéraires statiques, Border Gateway Protocol, les mises à jour des itinéraires, ainsi que des diagnostics supplémentaires. |
| **Journaux de diagnostics IKE** | Diagnostics spécifiques à IKE pour les connexions IPsec. |

### <a name="point-to-site-vpn-gateways"></a>Passerelles VPN point à site

Les diagnostics suivants sont disponibles pour les passerelles VPN point à site Azure :

| Métrique | Description|
| --- | --- |
| **Journaux de diagnostic de passerelle** | Diagnostics spécifiques à la passerelle, tels que l’intégrité, la configuration, les mises à jour du service, ainsi que d’autres diagnostics. |
| **Journaux de diagnostics IKE** | Diagnostics spécifiques à IKE pour les connexions IPsec.|
| **Journaux de diagnostics PS2** | Il s’agit des événements de client et de configuration P2S (point à site) du VPN des utilisateurs. Il s’agit notamment de la connexion/déconnexion des clients, de l’allocation d’adresses de clients VPN, ainsi que d’autres Diagnostics.|

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Afficher les journaux de diagnostic

Les étapes suivantes vous aident à localiser et à afficher les diagnostics :

1. Dans le portail, accédez à votre ressource Virtual WAN. Dans la section **Vue d’ensemble** de la page Virtual WAN du portail, sélectionnez **Essentiels** pour développer la vue et obtenir des informations sur le groupe de ressources. Copiez les informations du groupe de ressources.

   :::image type="content" source="./media/monitor-virtual-wan/3.png" alt-text="Capture d’écran montrant la section « Vue d’ensemble » avec une flèche pointant vers le bouton « Copier ».":::

2. Accédez à **Surveiller** à partir de la barre de recherche et, dans la section Paramètres, sélectionnez **Paramètres de diagnostic**, puis entrez le groupe de ressources, le type de ressources et les informations sur les ressources. Il s’agit des informations relatives au groupe de ressources que vous avez copiées à l’étape 2 de la section [Afficher les métriques de passerelle](#metrics-steps), plus haut dans cet article.

   :::image type="content" source="./media/monitor-virtual-wan/4.png" alt-text="Capture d’écran montrant la section « Surveillance » avec une flèche pointant vers la liste déroulante « Ressource ».":::

3. Dans la page des résultats, sélectionnez **+ Ajouter un paramètre de diagnostic**, puis sélectionnez une option. Vous pouvez choisir d’envoyer à Log Analytics, de diffuser vers un Event Hub ou de simplement archiver dans un compte de stockage.

   :::image type="content" source="./media/monitor-virtual-wan/5.png" alt-text="page métriques":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Exemple de requête Log Analytics

Les journaux se trouvent dans **Espace de travail Log Analytics Azure**. Vous pouvez configurer une requête dans Log Analytics. L’exemple suivant contient une requête visant à obtenir des diagnostics d’itinéraire site à site.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Remplacez les valeurs ci-dessous, après le caractère **==** , si nécessaire.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Journaux d’activité

Les entrées du **journal d’activité** sont recueillies par défaut et peuvent être affichées dans le portail Azure. Vous pouvez utiliser les journaux d’activité Azure (anciennement *journaux des opérations* et *journaux d’audit*) pour afficher toutes les opérations soumises à votre abonnement Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment superviser les métriques et les journaux de Pare-feu Azure, consultez le [Tutoriel : Surveiller les journaux d’activité de pare-feu Azure](../firewall/firewall-diagnostics.md).
* Pour en savoir plus sur les métriques dans Azure Monitor, consultez [Métriques dans Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
