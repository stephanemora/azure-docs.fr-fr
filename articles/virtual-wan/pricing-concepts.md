---
title: À propos de la tarification Virtual WAN
titleSuffix: Azure Virtual WAN
description: Cet article répond aux questions fréquentes sur la tarification de Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: cherylmc
ms.custom: references_pricing
ms.openlocfilehash: 853005cdc9e44357661cdb0b3b50e7f6ba5a0bbf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730099"
---
# <a name="about-virtual-wan-pricing"></a>À propos de la tarification Virtual WAN

Azure Virtual WAN est un service de mise en réseau qui combine un grand nombre de fonctionnalités de mise en réseau, de sécurité et de routage pour fournir une interface opérationnelle unique. Ces fonctionnalités sont les suivantes : connectivité de branche (via une automatisation de la connectivité à partir d’appareils partenaires d’Azure Virtual WAN, tels que SD-WAN ou un CPE VPN), connectivité VPN site à site, connectivité VPN d’utilisateurs distants (point à site), connectivité privée (ExpressRoute), connectivité intra-cloud (connectivité transitive pour les réseaux virtuels), interconnectivité ExpressRoute de VPN, routage, Pare-feu Azure et chiffrement de connexion privée.

Cet article présente trois scénarios couramment déployés avec Azure Virtual WAN et des estimations de prix standard pour les déploiements en fonction des tarifs indiqués. En outre, il peut y avoir de nombreux autres scénarios où le service Virtual WAN peut s’avérer utile.

> [!IMPORTANT]
> La tarification indiquée dans cet article est fournie à titre d’exemple uniquement. 
>  * La tarification peut changer à tout moment. Pour connaître les informations de tarification actuelles, consultez la page [Tarification de Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/). 
>  * Les frais inter-hub (hub vers hub) ne s’affichent pas dans la page de tarification Virtual WAN en raison des frais entre régions (intra/inter-continentaux). Pour plus d'informations, consultez [Frais de transfert de données Azure](https://azure.microsoft.com/pricing/details/bandwidth/).
>

## <a name="pricing-components"></a><a name="pricing"></a>Composants de la tarification

Le diagramme suivant montre les itinéraires de données typiques dans un réseau qui implique Virtual WAN ainsi que les différents composants de la tarification par heure et par Go.

:::image type="content" source="./media/pricing-concepts/data-routes.png" alt-text="Diagramme des itinéraires de données." lightbox="./media/pricing-concepts/data-routes.png":::

| Valeur | Scénario| Coût horaire | Coût par Go |
| --- | --- | --- | --- |
| 1 | Transfert de données entre un réseau virtuel spoke et une branche VPN de site à site via un hub vWAN Standard dans la région USA Est|Heure de déploiement (0,25 $/h) + unité d’échelle S2S VPN (0,261 $/h) + unité de connexion VPN S2S (0,05 $/h) = 0,561 $/h|VNet Peering (sortant) (0,01 $/Go) + Zone 1 Sortante Standard (0,087 $/Go) = 0,097 $/Go|
| 1'|Transfert de données entre une branche VPN de site à site et un réseau virtuel spoke via un hub vWAN Standard dans la région USA Est|Heure de déploiement (0,25 $/h) + unité d’échelle S2S VPN (0,261 $/h) + unité de connexion VPN S2S (0,05 $/h) = 0,561 $/h|VNet Peering (entrant) (0,01 $/Go) = 0,01 $/Go|
|2|Transfert de données entre une branche VPN de site à site via un hub vWAN Standard et une autre branche VPN de site à site dans la région USA Est|Heure de déploiement (0,25 $/h) + unité d’échelle S2S VPN (0,261 $/h) + unité de connexion VPN S2S (2 x 0,05 $/h) = 0,611 $/h|Zone 1 sortante Standard (0,087 $/Go) = 0,087 $/Go|
|2'|Transfert de données entre une branche VPN de site à site via un hub vWAN standard et le centre de données connecté ExpressRoute/HQ dans la région USA Est|Heure de déploiement (0,25 $/h) + unité d’échelle S2S VPN (0,261 $/h) + unité de connexion VPN S2S (0,05 $/h) + unité d’échelle ExpressRoute (0,42 $/h) + unité de connexion ExpressRoute (0,05 $/h) = 1,03 $/h|Zone 1 sortante limitée ExpressRoute (0,025 $/Go) = 0,025 $/Go|
|3|Transfert de données entre une branche VPN de site à site via un hub vWAN standard et le centre de données connecté ExpressRoute/HQ dans la région USA Est|Heure de déploiement (0,25 $/h) + unité d’échelle S2S VPN (0,261 $/h) + unité de connexion VPN S2S (0,05 $/h) + unité d’échelle ExpressRoute (0,42 $/h) + unité de connexion ExpressRoute (0,05 $/h) = 1,03 $/h|Zone 1 sortante limitée ExpressRoute (0,025 $/Go) = 0,025 $/Go|
|4|Transfert de données entre un réseau virtuel spoke et un centre de données ExpressRoute connecté via un hub vWAN Standard dans la région USA Est|Heure de déploiement (0,25 $/h) + unité d’échelle ExpressRoute (0,42 $/h) + unité de connexion ExpressRoute (0,05 $/h) = 0,72 $/h|VNet Peering (sortant) (0,01 $/Go) + Zone 1 sortante limitée ExpressRoute (0,025 $/Go) = 0,035 $/Go|
|4'|Transfert de données entre un centre de données ExpressRoute connecté/HQ et un réseau virtuel spoke via un hub vWAN Standard dans la région USA Est|Heure de déploiement (0,25 $/h) + unité d’échelle ExpressRoute (0,42 $/h) + unité de connexion ExpressRoute (0,05 $/h) = 0,72 $/h|VNet Peering (entrant) (0,01 $/Go) = 0,01 $/Go|
|4"|Transfert de données entre un centre de données connecté ExpressRoute/HQ et un réseau virtuel spoke distant via un hub vWAN Standard en Europe|Heure de déploiement (2 x 0,25 $/h) + unité d’échelle ExpressRoute (0,42 $/h) + unité de connexion ExpressRoute (0,05 $/h) = 0,97 $/h|VNet Peering (entrant) (0,01 $/Go) + traitement des données du hub (Europe) (0,02 $/Go) + transfert de données interrégion (USA Est vers Europe) (0,05 $/Go) = 0,08 $/Go|
|5|Transfert de données entre un réseau virtuel spoke et un autre réseau virtuel spoke via un hub vWAN Standard dans la région USA Est|Heure de déploiement (0,25 $/h) = 0,25 $/h|VNet Peering (sortant + entrant) (2 x 0,01 $/Go) + traitement des données du hub (0,02 $/Go) = 0 $. 04/Go|
|6|Transfert de données entre un réseau virtuel spoke connecté à un hub dans la région USA Est et un autre réseau virtuel spoke en Europe (région différente) connecté à un hub en Europe|Heure de déploiement (2 x 0,25 $/h) = 0,50 $/h|VNet Peering (sortant + entrant) (2 x 0,01 $/Go) + traitement des données du hub (2 x 0,02 $/Go) + transfert de données interrégion (USA Est vers Europe) (0,05 $/Go) = 0 $. 11/Go|
|7|Transfert de données entre un réseau virtuel spoke et un VPN utilisateur (point à site) via un hub vWAN Standard en Europe|Heure de déploiement (0,25 $/h) + unité d’échelle P2S VPN (0,261 $/h) + unité de connexion VPN P2S (0,0125 $/h) = 0,524 $/h|VNet Peering (sortant) (0,01 $/Go) + Zone 1 Sortante Standard (0,087 $/Go) = 0,097 $/Go|
|7'|Transfert de données entre un VPN utilisateur (point à site) et un réseau virtuel spoke via un hub vWAN Standard en Europe|Heure de déploiement (0,25 $/h) + unité d’échelle P2S VPN (0,261 $/h) + unité de connexion VPN P2S (0,0125 $/h) = 0,524 $/h|VNet Peering (entrant) (0,01 $/Go) = 0,01 $/Go|
|8|Transfert de données entre une branche SD-WAN via une appliance virtuelle réseau dans un hub à Singapour et une autre branche SD-WAN dans la même région|Heure de déploiement de hub vWAN Standard (0,25 $/h) + unité d’infrastructure d’appliance virtuelle réseau (0,25 $/h) = 0,50 $/h<br> *Des frais de licence tiers supplémentaires peuvent s’appliquer|Zone 2 sortante Standard (0,12 $/Go) = 0,12 $/Go|

## <a name="common-topology-scenarios"></a><a name="topologies"></a>Scénarios de topologie courants

### <a name="microsoft-global-backbone-wan"></a><a name="global"></a>WAN du réseau principal mondial de Microsoft

Dans ce scénario, vous créez des hubs régionaux automatiques entièrement maillés dans le monde entier, ce qui sert de réseau principal Microsoft pour la connectivité du trafic dans le monde entier. La connectivité classique implique des points de terminaison tels que des branches (VPN de site à site ou SD-WAN), des utilisateurs distants (VPN de point à site) et des circuits privés (ExpressRoute). Tout cela s’appuie sur le réseau principal Microsoft pour transporter le trafic mondialement.

**Diagramme du scénario 1 : WAN du réseau principal mondial de Microsoft**

:::image type="content" source="./media/pricing-concepts/global-backbone.png" alt-text="Diagramme du WAN du réseau principal mondial de Microsoft." lightbox="./media/pricing-concepts/global-backbone.png":::

#### <a name="alternatives"></a>Autres solutions

* Vous pouvez également choisir de disposer d’un hub vWAN sécurisé (incluant un pare-feu) qui devient un point central pour le routage et les besoins de sécurité dans chaque région mondiale.

### <a name="software-defined-wide-area-network-sd-wan"></a><a name="sdwan"></a>Réseau étendu (SD-WAN) à définition logicielle

Dans ce scénario, les magasins qui migrent vers la technologie SD-WAN utilisent des hubs vWAN pour la terminaison de magasin automatisée pour accéder aux ressources sur Azure et en local (centre de données). Les magasins sont connectés via des tunnels VPN pour envoyer le trafic de manière sécurisée via Internet via le hub vers le centre de données local ou pour accéder aux applications partagées sur Azure.

**Diagramme du scénario 2 : WAN à définition logicielle (SD-WAN)**

:::image type="content" source="./media/pricing-concepts/sd-wan.png" alt-text="Diagramme de SD-WAN." lightbox="./media/pricing-concepts/sd-wan.png":::

#### <a name="alternatives"></a>Autres solutions

* Vous pouvez choisir d’utiliser une appliance virtuelle réseau tierce dans le hub et de la connecter aux magasins et centres de données.

* Vous pouvez également choisir de disposer d’un hub vWAN sécurisé (incluant un pare-feu) qui devient un point central pour le routage et les besoins de sécurité.

### <a name="remote-user-connectivity"></a><a name="remote"></a>Connectivité de l’utilisateur distant

Dans ce scénario, les sessions des utilisateurs distants se terminent sur les hubs vWAN. Il peut s’agir d’utilisateurs distants et/ou de sessions Azure Virtual Desktop à partir de réseaux virtuels. Actuellement, 100 000 utilisateurs sont pris en charge sur chaque Hub.

Le diagramme suivant montre des connexions VPN de point à site sur des connexions de réseau virtuel pour le trafic chiffré dans les tunnels entre les réseaux virtuels spoke et le hub vWAN. Vous pouvez également choisir d’utiliser des connexions d’appairage de réseaux virtuels entre différents réseaux virtuels spoke pour bénéficier d’une connectivité directe. Par exemple, entre des réseaux virtuels spoke partagés et VDI.

**Diagramme du scénario 3 : connectivité des utilisateurs distants**

:::image type="content" source="./media/pricing-concepts/remote.png" alt-text="Diagramme de la connectivité des utilisateurs distants" lightbox="./media/pricing-concepts/remote.png":::

## <a name="data-flow-calculations"></a>Calculs de flux de données

> [!IMPORTANT]
> La tarification indiquée dans cet article est fournie à titre d’exemple uniquement et peut faire l’objet de modifications. Pour accéder aux informations les plus récentes sur la tarification, consultez la [page de tarification Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

### <a name="microsoft-global-backbone-wan-calculation"></a>Calcul : WAN du réseau principal mondial de Microsoft

Dans ce scénario, nous avons opté pour un total de données de 8 To transmises via le réseau mondial via les hubs vWAN, comme indiqué dans le diagramme ci-dessous. Le montant total des coûts de transfert de données est égal à 600 $ pour ceci (somme de tous les coûts de flux de données dans le diagramme ci-dessous, en supposant des frais facturés pour ExpressRoute). Le montant total des coûts de hub (3 unités d’échelle + 3 unités de connexion (ER) + 3 déploiements de hub) est égal à 1 534 $.

**Diagramme du scénario 1 : Calcul - WAN du réseau principal mondial de Microsoft**

:::image type="content" source="./media/pricing-concepts/global-backbone-pricing.png" alt-text="Diagramme de la tarification du réseau principal de Microsoft." lightbox="./media/pricing-concepts/global-backbone-pricing.png":::

| Valeur | Calcul |
| --- | --- |
|Hub S2S VPN Singapour |(1 unité d’échelle S2S VPN (0,361 $/h) + 1 unité de connexion (0,05 $/h)) x 730 heures = 300 $ par mois|
|Hub ExpressRoute US E |(1 unité d’échelle ER (0,42 $/h) + 1 unité de connexion (0,05 $/h)) x 730 heures = 343 $ par mois|
|Hub ExpressRoute EU|(1 unité d’échelle ER (0,42 $/h) + 1 unité de connexion (0,05 $/h)) x 730 heures = 343 $ par mois|
|Coût de déploiement d’un Hub Standard |3 hubs x 730 heures x 0,25 $/h = 548 $ par mois|
|**Total**|**1 534 $** par mois |

### <a name="software-defined-wide-area-network-sd-wan-calculation"></a>Calcul - réseau étendu (SD-WAN) à définition logicielle

Dans ce scénario, nous avons opté pour un total de données de 12 To transmises via le hub vWAN, comme indiqué dans le diagramme ci-dessous dans la région USA Est. Le montant total des coûts de transfert de données est égal à 434 $ (somme de tous les coûts de flux de données indiqués ci-dessous ; inclut les frais de traitement des hubs, l’homologation, la bande passante, les coûts de transfert de données ER limités) et le montant total des coût du hub (2 unités d’échelle + 3 unités de connexion (2 S2S, 1 ER) + 1 hub de déploiement) est égal à 863 $.

**Diagramme du scénario 2 : Calcul - WAN à définition logicielle (SD-WAN)**

:::image type="content" source="./media/pricing-concepts/sd-wan-pricing.png" alt-text="Diagramme de la tarification SD-WAN." lightbox="./media/pricing-concepts/sd-wan-pricing.png":::

| Valeur | Calcul |
| --- | --- |
|S2S VPN (branches) |(1 unité d’échelle S2S VPN (0,361 $/h) + 2 unités de connexion (2 x 0,05 $/h) x 730 heures = 337 $ par mois|
|Hub ExpressRoute (HQ) |(1 unité d’échelle ER (0,42 $/h) + 1 unité de connexion (0,05 $/h)) x 730 heures = 343 $ par mois|
|Coût de déploiement d’un Hub Standard|1 hub x 730 heures x 0,25 $/h = 183 $ par mois|
|**Total**|**863 $** par mois|

### <a name="remote-user-connectivity-calculation"></a>Calcul de la connectivité des utilisateurs distants

Dans ce scénario, nous avons opté pour un total de données de 15 To transmises via le réseau dans la région USA Est. Le montant total des coûts de transfert de données est égal à 405 $ (incluant les frais de traitement du hub, l’homologation, la bande passante, les frais de transfert de données ER limitées) et le montant total des coûts du hub (2 unités d’échelle + 2 unités de connexion + 2 déploiements de hub) est égal à 708 $.

**Diagramme du scénario 3 : calcul Connectivité des utilisateurs distants**
    
:::image type="content" source="./media/pricing-concepts/remote-pricing.png" alt-text="Diagramme de la tarification des utilisateurs distants." lightbox="./media/pricing-concepts/remote-pricing.png":::

| Valeur | Calcul |
| --- | --- |
|Hub ExpressRoute (HQ) |(1 unité d’échelle ER (0,42 $/h) + 1 unité de connexion (0,05 $/h)) x 730 heures = 343 $ par mois |
|Coût de déploiement d’un Hub Standard| 2 hubs x 730 heures x 0,25 $/h = 365 $ par mois |
|**Total** |**708 $** par mois |

## <a name="pricing-faq"></a>FAQ sur la tarification

> [!IMPORTANT]
> La tarification indiquée dans cet article est fournie à titre d’exemple uniquement et peut faire l’objet de modifications. Pour accéder aux informations les plus récentes sur la tarification, consultez la [page de tarification Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Qu’est-ce qu’une unité d’échelle ?

Une **unité d’échelle** est une unité de capacité agrégée de site à site (S2S), de point à site (P2S) et ExpressRoute (ER) dans un hub virtuel. Par exemple :

* **1 unité d’échelle S2S VPN** implique la capacité totale de la passerelle VPN de 500 Mbits/s (des instances doubles sont déployées pour des raisons de résilience) dans un hub virtuel, pour un coût horaire de 0,361 $.

* **1 unité d’échelle ER** implique un total de 2 Gbit/s de passerelle ER dans un hub virtuel, pour un coût de 0,42 $/h.

* **5 unités d’échelle ER** impliquent un total de 10 Gbit/s de passerelle ER au sein d’un réseau virtuel de hub virtuel facturé à 0,42*5 $/h. Paliers ER de 0,25 $/h de 6 à 10 unités d’échelle.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit">Qu’est-ce qu’une unité de connexion ?</a>

Une **unité de connexion** s’applique à tout point de terminaison local/non-Microsoft se connectant aux passerelles Azure. Pour un VPN de site à site, cette valeur implique des branches. Pour un VPN utilisateur (point à site), cette valeur implique des utilisateurs distants. Pour ExpressRoute, cette valeur implique des connexions de circuit ExpressRoute.<br>Par exemple :

* Une connexion de branche se connectant à Azure VPN dans un hub virtuel coûte de 0,05 $/h. Par conséquent, 100 connexions de branche se connectant à un hub virtuel Azure coûtent 0,05 $*100/h.

* Deux connexions de circuit ExpressRoute qui se connectent à un hub virtuel coûtent 0,05 $*2/h.

* Trois connexions utilisateur distantes se connectant à la passerelle P2S Azure Virtual Hub coûtent 0,03 $*3/h.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Comment les frais de transfert de données sont-ils calculés ?

* Le trafic entrant dans Azure n’est pas facturé. Le trafic qui quitte Azure (via VPN, ExpressRoute ou les connexions VPN utilisateur de point à site) est soumis aux [frais de transfert de données Azure](https://azure.microsoft.com/pricing/details/bandwidth/) standard ou, pour ExpressRoute, à la [tarification ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

* Les frais d’homologation s’appliquent lorsqu’un réseau virtuel connecté à un hub vWAN envoie ou reçoit des données. Pour plus d’informations, consultez [Tarification de Réseau virtuel Microsoft Azure](https://azure.microsoft.com/pricing/details/virtual-network/).

* Pour les frais de transfert de données entre un hub Virtual WAN et un hub Virtual WAN distant ou un réseau virtuel dans une région différente de celle du hub source, les frais de transfert de données s’appliquent au trafic quittant le hub. Exemple : Le trafic qui part d’un hub de la région USA Est est facturé 0,02 $/Go vers un hub USA Ouest. Il n’y a pas de frais pour le trafic entrant dans le hub USA Ouest. Tout le trafic de hub à hub est soumis à des frais inter-régions (intra/Inter-continentaux) [Frais de transfert de données Azure](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Quelle est la différence entre un tarif hub standard et les frais de traitement de hub standard ?

Il existe deux types de réseaux Virtual WAN :

* Un **WAN virtuel de base**, dans lequel les utilisateurs peuvent déployer plusieurs hubs et utiliser une connectivité VPN de site à site. Le WAN virtuel de base ne dispose pas de fonctionnalités avancées telles que des hubs entièrement maillés, la connectivité ExpressRoute, la connectivité VPN utilisateur/point à site, la connectivité de réseau virtuel à réseau virtuel et la connectivité de transit ExpressRoute ou le Pare-feu Azure. Aucun frais de base ou frais de traitement des données n’est facturé pour les hubs dans un Virtual WAN de base.

* Le **WAN virtuel standard** propose des fonctionnalités avancées telles que des hubs entièrement maillés, la connectivité ExpressRoute, la connectivité VPN utilisateur/point à site, la connectivité de réseau virtuel à réseau virtuel et la connectivité de transit ExpressRoute et le Pare-feu Azure. L’ensemble du routage du hub virtuel est fourni par un routeur qui active plusieurs services dans un hub. Il y a des frais de base pour le hub, facturé à 0,25 $/h. Il y a également un coût pour le traitement des données dans le routeur de hub virtuel pour la connectivité de transit de réseau virtuel à réseau virtuel. Les frais de traitement des données dans le routeur du hub virtuel ne sont pas applicables pour les transferts de branche à branche (scénario 2, 2', 3) ou les transferts de réseau virtuel à branche via le même hub vWAN (scénario 1, 1'), comme indiqué dans les [composants de tarification](#pricing). 

## <a name="next-steps"></a>Étapes suivantes

* Pour connaître la tarification actuelle, consultez la page [Tarification de Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).


