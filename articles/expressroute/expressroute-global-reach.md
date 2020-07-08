---
title: 'Azure ExpressRoute : Se connecter à Microsoft Cloud à l’aide de Global Reach'
description: Cet article présente ExpressRoute Global Reach.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5022efacf33a2c07f1388666b5de26ee86322739
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636731"
---
# <a name="expressroute-global-reach"></a>Service Global Reach d’ExpressRoute
ExpressRoute vous permet de connecter vos réseaux locaux à Microsoft Cloud de manière privée et résiliente. Vous pouvez accéder à de nombreux services cloud Microsoft, comme Azure et Office 365, à partir de votre centre de données privé ou de votre réseau d’entreprise. Par exemple, vous pouvez avoir une succursale à San Francisco avec un circuit ExpressRoute dans la Silicon Valley, et une autre à Londres avec un circuit ExpressRoute dans cette même ville. Les deux succursales peuvent disposer d’une connectivité haut débit avec les ressources Azure situées dans les régions USA Ouest et Royaume-Uni Sud, mais elles ne peuvent pas échanger de données directement entre elles. En d’autres termes, 10.0.1.0/24 peut envoyer des données à 10.0.3.0/24 et 10.0.4.0/24, mais PAS à 10.0.2.0/24.

![without][1]

Avec **ExpressRoute Global Reach**, vous pouvez associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux. Dans l’exemple ci-dessus, grâce à l’ajout d’ExpressRoute Global Reach, votre bureau de San Francisco (10.0.1.0/24) peut échanger directement des données avec le bureau de Londres (10.0.2.0/24) par le biais des circuits ExpressRoute existants et du réseau mondial de Microsoft. 

![with][2]

## <a name="use-case"></a>Cas d’utilisation
ExpressRoute Global Reach est conçu pour compléter l’implémentation du réseau étendu (WAN) de votre fournisseur de services et connecter vos succursales à travers le monde. Par exemple, si votre fournisseur de services opère principalement aux États-Unis et a connecté toutes vos branches dans ce pays, mais qu’il n’opère ni au Japon ni à Hong Kong (R.A.S.), ExpressRoute Global Reach vous permet de travailler avec un fournisseur de services local, et Microsoft connecte vos branches situées là-bas à celles figurant aux États-Unis à l’aide d’ExpressRoute et de notre réseau mondial.

![Cas d’usage][3]

## <a name="availability"></a>Disponibilité 
ExpressRoute Global Reach est actuellement pris en charge dans les pays et dans le territoire suivants.

* Australie
* Canada
* France
* Allemagne
* Hong Kong (R.A.S.)
* Irlande
* Japon
* Corée du Sud
* Pays-Bas
* Nouvelle-Zélande
* Norvège
* Singapour
* Suède
* Suisse
* Royaume-Uni
* États-Unis

Vos circuits ExpressRoute doivent être créés sur les [sites de peering ExpressRoute](expressroute-locations.md) dans le territoire ou les pays indiqués ci-dessus. Pour activer ExpressRoute Global Reach entre [différentes régions](expressroute-locations.md), vous devez disposer de circuits Premium.

## <a name="next-steps"></a>Étapes suivantes
1. [Consulter les Questions fréquentes (FAQ) sur Global Reach](expressroute-faqs.md#globalreach)
2. [Découvrir comment activer Global Reach](expressroute-howto-set-global-reach.md)
3. [Découvrir comment lier un circuit ExpressRoute à votre réseau virtuel](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "schéma sans portée globale"
[2]: ./media/expressroute-global-reach/2.png "schéma avec portée globale"
[3]: ./media/expressroute-global-reach/3.png "cas d’usage de global reach"
