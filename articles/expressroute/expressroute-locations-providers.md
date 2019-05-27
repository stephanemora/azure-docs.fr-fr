---
title: 'Emplacements et fournisseurs de connectivité : Azure ExpressRoute | Microsoft Docs'
description: Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure. Trié par emplacement.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2019
ms.author: pareshmu
ms.openlocfilehash: 8253610bd0c329c952442044586236acc56d4957
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978099"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partenaires ExpressRoute et emplacements d’homologation

> [!div class="op_single_selector"]
> * [Emplacements par fournisseur](expressroute-locations.md)
> * [Fournisseurs par emplacement](expressroute-locations-providers.md)


Les tables de cet article offrent des informations sur les fournisseurs de connectivité ExpressRoute, la couverture géographique ExpressRoute, les services cloud Microsoft pris en charge via ExpressRoute et les intégrateurs système ExpressRoute.

## <a name="partners"></a>Fournisseurs de connectivité ExpressRoute
ExpressRoute est pris en charge dans tous les emplacements et régions Azure. La carte ci-dessous fournit une liste des régions Azure et des emplacements ExpressRoute. Les emplacements ExpressRoute se réfèrent à ceux où Microsoft s’associe à plusieurs fournisseurs de services.

![Carte des emplacements][0]

Vous aurez accès aux services Azure dans toutes les régions au sein d’une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique
Le tableau ci-dessous fournit une carte des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

| **Région géopolitique** | **Zone** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- | --- |
| **Secteur public australien** | 1 | Australie Centre, Australie Centre 2 |Canberra, Canberra2 |
| **Europe** | 1 |France Centre, France Sud, Europe Nord, Europe Ouest, Royaume-Uni Ouest, Royaume-Uni Sud |Amsterdam, Amsterdam2, Dublin, Frankfurt, London, London2, Marseille, Newport(Wales), Paris, Zurich |
| **Amérique du Nord** | 1 |USA Est, USA Ouest, USA Est 2, USA Ouest 2, USA Centre, USA Centre Sud, USA Centre Nord, USA Centre-Ouest, Canada Centre, Canada Est |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montréal, Québec, Toronto |
| **Asie** | 2 |Asie Est, Asie Sud-Est |Hong Kong (r.a.s.), Kuala Lumpur, Singapour, singapour2, Taipei |
| **Australie** | 2 |Australie Sud-Est | Australie Est |Melbourne, Perth, Sydney | 
| **Inde** | 2 |Inde Ouest, Inde Centre, Inde Sud |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japon** | 2 |Japon Ouest, Japon Est |Osaka, Tokyo |
| **Corée du Sud** | 2 |Centre de la Corée, Corée du Sud |Busan, Séoul|
| **ÉMIRATS ARABES UNIS** | 3 | Émirats Arabes Unis, États-Unis, Nord des Émirats Arabes Unis | Dubaï, Dubai2 |
| **Afrique du Sud** | 3 |Afrique du Sud Ouest, Nord de l’Afrique du Sud |Le Cap, Johannesburg |
| **Amérique du Sud** | 3 |Brésil Sud |São Paulo |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Régions et limites géopolitiques pour les clouds nationaux
Le tableau ci-dessous fournit des informations sur les régions et les limites géopolitiques et des clouds nationaux.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- |
| **Cloud du gouvernement des États-Unis** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginie, US DoD Centre, US DoD Est  |Chicago, Dallas, New York, Seattle, Phoenix, San Antonio, Silicon Valley, Washington DC |
| **Chine Est** |Chine Est, Chine Est2 |Shanghai, Shanghai2 |
| **Chine Nord** |Chine Nord, Chine Nord2 |Beijing, Beijing2 |
| **Allemagne** |Allemagne centrale, Allemagne de l’est |Berlin, Francfort |

La connectivité entre les régions géopolitiques n’est pas prise en charge dans la référence ExpressRoute Standard. Vous devez activer le module complémentaire ExpressRoute Premium pour prendre en charge la connectivité globale. La connectivité à des environnements de cloud nationaux n’est pas prise en charge. En cas de besoin, vous pouvez collaborer avec votre fournisseur de connectivité.

## <a name="locations"></a>Emplacements de fournisseur de connectivité

Le tableau suivant présente les emplacements de connectivité et les fournisseurs de services pour chaque emplacement. Si vous souhaitez afficher les fournisseurs de service et les lieux pour lesquels ils peuvent fournir le service, consultez la page [Lieux par fournisseur de services](expressroute-locations.md#locations). 

**Les régions Azure locales** sont ceux qui [ExpressRoute Local](expressroute-faqs.md) dans chaque emplacement d’homologation peut accéder. **n/a** indique que ExpressRoute Local n’est pas disponible à cet emplacement d’homologation.


### <a name="production-azure"></a>Production Azure
| **Lieu** | **Propriétaire de l’emplacement d’homologation** | **Régions Azure locales** | **Fournisseurs de services** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Europe Ouest | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Europe Ouest | Allemagne-CIX, Interxion |
| **Atlanta** | Equinix | n/a | Equinix, Megaport |
| **Busan** |LG CNS | Corée du Sud | LG CNS |
| **Canberra** | CDC | Centre de l’Australie | CDC |
| **Canberra2** | CDC | Centre de l’Australie 2| CDC |
| **Le Cap** | Teraco | Afrique du Sud Ouest | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Inde Sud | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Inde Sud | Airtel |
| **Chicago** | Equinix | USA Centre Nord | Aryaka Networks, AT & T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global limitée, Sprint, Telia transporteur, Verizon, Zayo |
| **Dallas** | Equinix | n/a | Aryaka Networks, AT & T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona réseaux, Telmex Uninet, Telia transporteur, Verizon, Zayo|
| **Denver** | CoreSite | USA Centre-Ouest | CoreSite, Megaport, Zayo |
| **Dubai** | Émirats arabes unis Etisalat | Nord des Émirats arabes unis | Émirats arabes unis Etisalat |
| **Dubai2** | unité de base datamena | Nord des Émirats arabes unis | unité de base datamena |
| **Dublin** | Equinix | Europe Nord | Colt, eir, Equinix, Interxion, Megaport |
| **Francfort** | Interxion | n/a | Allemagne-CIX, Interxion |
| **Hong Kong (r.a.s.)** | Equinix | Asie Est | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Johannesburg** | Teraco | Afrique du Sud Nord | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Kuala Lumpur** | TIME dotCom | n/a | TIME dotCom |
| **Las Vegas** | Échanger | n/a | CenturyLink Cloud Connect, Megaport |
| **Londres** | Equinix | Sud du Royaume-Uni | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Londres2** | Telehouse | Sud du Royaume-Uni | IX Reach, Equinix |
| **Los Angeles** | CoreSite | n/a | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |Interxion | France Sud | Interxion |
| **Melbourne** | NextDC | Australie Sud-Est | AARNet, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | n/a | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montréal** | Cologix | n/a | Bell Canada, Cologix, Telus, Zayo |
| **Mumbai** | Tata Communications | Inde Ouest | CloudXchange global (GCX), Jio de dépendance, Sify, Tata Communications, |
| **Mumbai2** | Airtel | Inde Ouest | Airtel, Sify, Vodafone Idea |
| **New York** | Equinix | n/a | Provoqués Cloud vous connecter, Coresite, Equinix, InterCloud, Megaport, paquet, Zayo |
| **Newport (Nouvelle-Galles du Sud)** | Next Generation Data | Ouest du Royaume-Uni | British Telecom, Level 3 Communications, Next Generation Data |
| **Osaka** | Equinix | Japon Ouest | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** | Interxion | France Centre | COLT, Equinix, Intercloud, Interxion, Orange, Telia transporteur, Zayo |
| **Perth** | NextDC | n/a | Megaport, NextDC |
| **Québec** | 4Degrees | Est du Canada | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | USA Centre Sud | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brésil Sud | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | USA Ouest 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Zayo |
| **Séoul** | KINX | Centre de la Corée | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | USA Ouest | Aryaka Networks, AT & T NetBond, British Telecom, provoqués Cloud Connect, Comcast, Coresite, Equinix, InterCloud, IX atteindre, le paquet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Verizon, Zayo |
| **Silicon Valley2** | Coresite | USA Ouest | Coresite | 
| **Singapour** | Equinix | Asie du Sud-Est | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | Global Switch | Asie du Sud-Est | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Sydney** | Equinix | Australie Est | AARNet, AT & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, télécommunications TPG, Verizon |
| **Taipei** | Chief Telecom | n/a | Chief Telecom, FarEasTone |
| **Tokyo** | Equinix | Japon Est | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Centre du Canada | AT & T NetBond, Bell Canada, provoqués Cloud vous connecter, Cologix, Equinix, IX atteindre Megaport, Telus, Zayo |
| **Washington DC** | Equinix | Est des États-Unis, est des États-Unis 2 | Aryaka Networks, AT & T NetBond, British Telecom, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona réseaux, NTT Communications, Orange, PacketFabric, Sprint, Tata Communications, Telia porteur, Verizon, Zayo |
| **Washington DC2** | Coresite | Est des États-Unis, est des États-Unis 2 |Coresite | 
| **Zurich** | Interxion | n/a | Interxion |

 **+** = bientôt disponible

### <a name="national-cloud-environments"></a>Environnements de cloud national

### <a name="us-government-cloud"></a>Cloud du gouvernement des États-Unis
| **Lieu** | **Fournisseurs de services** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT & T NetBond, provoqués Cloud se connecter |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington DC** |AT & T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Chine
| **Lieu** | **Fournisseurs de services** |
| --- | --- |
| **Beijing** |China Telecom |
| **Beijing2** | GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | GDS |

Pour plus d’informations, consultez [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Allemagne
| **Emplacement** | **Fournisseurs de services** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Francfort** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Connectivité via des fournisseurs Exchange
Si votre fournisseur de connectivité ne se trouve pas dans la liste des sections précédentes, vous pouvez quand même créer une connexion.

* Vérifiez auprès de votre fournisseur de connectivité s’il est connecté à l’un des échanges dans le tableau ci-dessous. Vous pouvez consulter les liens ci-dessous pour recueillir des informations supplémentaires sur les services proposés par les fournisseurs d’échange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Demandez à votre fournisseur de connectivité d’étendre votre réseau à l’emplacement d’homologation de votre choix.
  * Vérifiez que votre fournisseur de connectivité étend votre connectivité avec une haute disponibilité pour éviter tout point de défaillance unique.
* Commandez un circuit ExpressRoute avec échange en tant que fournisseur de connectivité pour se connecter à Microsoft.
  * Pour définir la connectivité, procédez de la manière décrite dans [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="c1partners"></a>Connectivité via d’autres fournisseurs de services
| **Lieu** | **Microsoft Exchange** | **Fournisseurs de connectivité** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telia |
| **Le Cap** | Teraco | MTN |
| **Chicago** | Equinix | Lightower, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Francfort** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **Hambourg** | Equinix | Cinia |
| **Hong Kong (r.a.s.)** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks, Telecity | Opérateur Bezeq International Ltd., CoreAzure, Epsilon télécommunications Limited, E exponentielle, HSO, nextgen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montréal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Paris** | Equinix | Proximus |
| **Québec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Equinix | Cox Business, Windstream |
| **Singapour** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies, Inc. Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Gtt Communications Inc, Epsilon Telecommunications Limited, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à intégrer ExpressRoute.

| **Continent** | **Intégrateurs système** |
| --- | --- |
| **Asie** |Avanade Inc., OneAs1a |
| **Australie** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europe** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Amérique du Nord** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Amérique du Sud** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
* Assurez-vous que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte géographique"
