---
title: Interconnexion avec la Chine à l’aide d’Azure Virtual WAN et d’un hub sécurisé
description: Découvrez plus d’informations sur la connectivité branche à branche scalable et automatisée de Virtual WAN, les régions disponibles et les partenaires.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d89a3c65eb8d8bffd4cf87160286d1905bd1ba5b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747607"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconnexion avec la Chine à l’aide d’Azure Virtual WAN et d’un hub sécurisé

Quand on examine des secteurs tels que l’automobile, la fabrication et la logistique, ou d’autres institutions telles les ambassades, la question de l’amélioration de l’interconnexion avec la Chine se pose souvent. Ces améliorations sont principalement pertinentes pour l’utilisation de services cloud tels qu’Office 365, les services globaux Azure ou l’interconnexion de branches à l’intérieur de la Chine avec une dorsale de client.

Dans la plupart des cas, les clients sont aux prises avec des latences élevées, une faible bande passante, une connexion instable et des coûts élevés de connexion vers l’extérieur de la Chine (par exemple, l’Europe ou les États-Unis).

L’une des raisons de ces difficultés est le « Grand Pare-feu de Chine », qui protège la partie chinoise d’Internet et filtre le trafic vers la Chine. Presque tout le trafic de la République populaire de Chine vers l’extérieur de la Chine, à l’exception des zones d’administration spéciales telles que Hong Kong (R.A.S.) et Macao (R.A.S.), passe par le Grand Pare-feu. Le trafic transitant par Hong Kong (R.A.S.) et Macao (R.A.S.) ne se heurte pas au Grand Pare-feu de plein fouet, mais est géré par un sous-ensemble de celui-ci.

![Interconnexion de fournisseurs](./media/interconnect-china/provider.png)

En utilisant Virtual WAN, un client peut établir une connexion plus performante et stable aux services de cloud computing Microsoft ainsi qu’une connexion à son réseau d’entreprise sans enfreindre la législation chinoise sur la cybersécurité.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Exigences et flux de travail

Si vous souhaitez rester conforme à la législation chinoise sur la cybersécurité, vous devez remplir certaines conditions.

Tout d’abord, vous devez collaborer avec un réseau et un fournisseur de services Internet titulaire d’une licence de fournisseur de contenu Internet (ICP, Internet Content Provider) pour la Chine. Dans la plupart des cas, vous vous retrouverez avec l’un des fournisseurs suivants :

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

Selon le fournisseur et vos besoins, vous devez maintenant acheter l’un des services de connectivité réseau suivants pour interconnecter vos branches en Chine.

* Réseau MPLS/IPVPN 
* WAN défini par logiciel (SDWAN)
* Accès dédié à Internet

Ensuite, vous devez convaincre ce fournisseur de s’ouvrir au Réseau Microsoft mondial et à son réseau de périmètre à Hong Kong (R.A.S.), pas Beijing ou Shanghai. En l’occurrence, Hong Kong est très important en raison de sa connexion physique et de son emplacement en Chine.

Si la plupart des clients pensent que l’utilisation de Singapour pour l’interconnexion constitue le meilleur choix car cela semble être plus proche de la Chine sur la carte, ce n’est pas vrai. Les cartes de fibre réseau révèlent que presque toutes les connexions réseau passent par Beijing, Shanghai et Hong Kong (R.A.S.). Ainsi, Hong Kong (R.A.S.) est un meilleur choix pour une interconnexion avec la Chine.

Les offres de services peuvent varier en fonction des fournisseurs. Le tableau ci-dessous présente des exemples de fournisseurs et les services qu’ils proposent, basés sur les informations disponibles au moment de la rédaction de cet article.

| Service | Exemples de fournisseurs |
| --- | --- |
| Réseau MPLS/IPVPN |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| Accès dédié à Internet | PCCW, Hong Kong Telecom, China Mobil|

Avec votre fournisseur, vous pouvez convenir de l’une des deux solutions suivantes à utiliser pour atteindre la dorsale mondiale Microsoft :

* Obtention d’une Microsoft Azure ExpressRoute se terminant à Hong Kong (R.A.S.). Ce serait le cas pour l’utilisation de MPLS/IPVPN. À l’heure actuelle, le seul ICP fournisseur de licences avec ExpressRoute jusqu’à Hong Kong (R.A.S.) est China Telecom Global. Ils peuvent cependant également être en contact avec d’autres fournisseurs Cloud Exchange tels que Megaport ou InterCloud. Pour plus d’informations, voir [Fournisseurs de connectivité ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Utilisation d’un accès Internet dédié directement à l’un des points d’échange Internet suivants, ou utilisation d’une interconnexion de réseaux privés.

La liste suivante répertorie les échanges Internet possibles à Hong Kong (R.A.S.) :

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Lors de l’utilisation de cette connexion, votre tronçon BGP suivant pour les services Microsoft doit être le Numéro de système autonome (NSA) Microsoft 8075. Si vous utilisez un emplacement ou une solution SDWAN unique, ce serait le choix de connexion.

Quoi qu’il en soit, nous vous recommandons d’avoir toujours un deuxième Breakout Internet régulier sur le continent chinois. Il s’agit de répartir le trafic entre le trafic de l’entreprise vers des services cloud tels que Microsoft 365 et Azure, et le trafic Internet réglementé par la loi.

Une architecture réseau conforme à l’intérieur de la Chine pourrait se présenter comme dans l’exemple suivant :

![Branches multiples](./media/interconnect-china/multi-branch.png)

Dans cet exemple, en ayant une interconnexion avec le Réseau Microsoft mondial à Hong Kong (R.A.S.), vous pouvez commencer à tirer parti de l’[architecture de transit mondial Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md) et de services supplémentaires tels que le hub Virtual WAN sécurisé Azure pour consommer des services et interconnecter vos branches et centres de données en dehors de Chine.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Communication de hub à hub

Dans cette section, nous utilisons une communication de hub à hub Virtual WAN pour l’interconnexion. Dans ce scénario, vous créez une ressource de hub Virtual WAN pour vous connecter à un hub Virtual WAN à Hong Kong (R.A.S.), dans d’autres régions de prédilection, dans une région où vous avez déjà des ressources Azure ou à l’emplacement de votre choix.

Une telle architecture pourrait se présenter comme suit :

![Exemple de WAN](./media/interconnect-china/sample.png)

Dans cet exemple, les branches de Chine se connectent au cloud Azure de Chine, et entre elles via des connexions VPN ou MPLS. Les branches qui doivent être connectées à des services globaux utilisent la technologie MPLS ou des services Internet connectés directement à Hong Kong (R.A.S.). Si vous souhaitez utiliser ExpressRoute à Hong Kong (R.A.S.) ainsi que dans l’autre région, vous devez configurer le service [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) pour interconnecter les deux circuits ExpressRoute.

Le service ExpressRoute Global Reach n’est pas disponible dans certaines régions. Si vous devez vous interconnecter avec le Brésil ou l’Inde, par exemple, vous devez vous appuyer sur des [fournisseurs Cloud Exchange ](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) pour les services de routage.

La figure ci-dessous illustre les deux exemples pour ce scénario.

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Breakout Internet sécurisé pour Office 365

Une autre considération est la sécurité du réseau ainsi que la journalisation du point d’entrée entre la Chine et la dorsale établie par le Virtual WAN d’un côté, et la dorsale du client de l’autre côté. Le plus souvent, il existe un besoin de breakout vers Internet à Hong Kong (R.A.S.) pour pouvoir atteindre directement le réseau de périmètre Microsoft ainsi que les serveurs Azure Front Door utilisés pour les services Microsoft 365.

Pour les deux scénarios avec Virtual WAN, vous utiliseriez le [hub sécurisé Azure Virtual WAN](../firewall-manager/secured-virtual-hub.md). Azure Firewall Manager vous permet de remplacer un hub Virtual WAN ordinaire par un hub sécurisé, puis de déployer et gérer un Pare-feu Azure à l’intérieur de ce hub.

La figure suivante illustre de scénario :

![Breakout Internet pour le trafic web et les services Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architecture et flux de trafic

En fonction de votre choix concernant la connexion à Hong Kong (R.A.S.), l’architecture peut changer légèrement. Cette section présente trois architectures disponibles dans différentes combinaisons avec VPN ou SDWAN et/ou ExpressRoute.

Toutes ces options utilisent un hub sécurisé Azure Virtual WAN pour la connectivité directe M365 à Hong Kong (R.A.S.). Ces architectures tiennent également compte des exigences de conformité pour [Office 365 multigéographique](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) et maintiennent le trafic à proximité du Front Door Office 365. Elles améliorent donc aussi l’utilisation de Microsoft 365 hors de Chine.

Lorsque vous utilisez Azure Virtual WAN avec des connexions Internet, chaque connexion peut bénéficier de services supplémentaires tels que le service [Microsoft Azure Peering Services (MAPS)](https://docs.microsoft.com/azure/peering-service/about). Le service MAPS a été conçu pour optimiser le trafic entrant dans le Réseau Microsoft mondial en provenance de fournisseurs de services Internet tiers.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Option 1 : SDWAN ou VPN

Cette section traite d’une conception qui utilise une connexion SDWAN ou VPN à Hong Kong (R.A.S.) et à d’autres branches. Cette option illustre l’utilisation et le flux de trafic en cas d’utilisation d’une connexion Internet pure sur les deux sites de la dorsale Virtual WAN. En l’occurrence, la connexion à Hong Kong (R.A.S.) est établie à l’aide d’un accès Internet dédié ou d’une solution SDWAN du fournisseur ICP. D’autres branches utilisent également des solutions Internet ou SDWAN pures.

![Trafic de Chine vers Hong Kong (R.A.S.)](./media/interconnect-china/china-traffic.png)

Dans cette architecture, chaque site est connecté au Réseau Microsoft mondial à l’aide d’un VPN et d’un Azure Virtual WAN. Le trafic entre les sites et Hong Kong (R.A.S.) transite via le réseau Microsoft et utilise uniquement une connexion Internet ordinaire sur le dernier kilomètre.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Option 2 : ExpressRoute et SDWAN ou VPN

Cette section présente une conception qui utilise ExpressRoute à Hong Kong, et d’autres branches avec des branches VPN/SDWAN. Cette option montre l’utilisation d’un circuit ExpressRoute se terminant à Hong Kong et d’autres branches connectées via SDWAN ou VPN. ExpressRoute à Hong Kong se limite actuellement à une brève liste de fournisseurs que vous pouvez trouver dans la liste des [partenaires ExpressRoute](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![ExpressRoute pour le trafic de Chine vers Hong Kong (R.A.S.)](./media/interconnect-china/expressroute.png)

Il existe également des options pour terminer l’ExpressRoute en provenance de Chine, par exemple, en Corée du Sud ou au Japon. Cependant, pour des raisons de conformité, de réglementation et de latence, Hong Kong est actuellement le meilleur choix.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Option 3 : ExpressRoute uniquement

Cette section présente une conception où ExpressRoute est utilisé pour Hong Kong et les autres branches. Cette option montre l’interconnexion à l’aide d’ExpressRoute aux deux extrémités. Ici, vous avez un flux de trafic différent de l’autre. Le trafic Microsoft 365 est acheminé vers le hub sécurisé Azure Virtual WAN, puis de là vers le réseau de périmètre Microsoft Edge et Internet.

Le trafic qui va vers les branches interconnectées ou de celles-ci vers les emplacements en Chine suit une approche différente au sein de cette architecture. Actuellement, Virtual WAN ne prend pas en charge le transit d’ExpressRoute à ExpressRoute. Le trafic profite d’ExpressRoute Global Reach ou de l’interconnexion tierce sans passer par le hub Virtual WAN. Il passe directement d’un périmètre d’entreprise Microsoft (MSEE) à un autre.

![Service Global Reach d’ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

Actuellement, ExpressRoute Global Reach n’est pas disponible dans chaque pays/région, mais vous pouvez configurer une solution utilisant Azure Virtual WAN.

Vous pouvez, par exemple, configurer un circuit ExpressRoute avec une homologation Microsoft, et connecter un tunnel VPN via cette homologation à Azure Virtual WAN. Vous réactivez ainsi le transit entre le VPN et ExpressRoute sans Global Reach et un fournisseur/service tiers tel que Megaport.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

* [Architecture du réseau de transit global avec Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Créer un hub Virtual WAN](virtual-wan-site-to-site-portal.md)

* [Configurer un hub sécurisé Virtual WAN](../firewall-manager/secure-cloud-network.md)

* [Vue d’ensemble de la préversion d’Azure Peering Service](https://docs.microsoft.com/azure/peering-service/about)
