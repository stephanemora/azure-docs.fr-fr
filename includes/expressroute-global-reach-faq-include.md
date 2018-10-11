---
title: Fichier Include
description: Fichier Include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 09/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b9c4cf6c90ef5507b318b4f13afb982aab151c79
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874066"
---
### <a name="what-is-expressroute-global-reach"></a>Présentation d’ExpressRoute Global Reach

ExpressRoute Global Reach est un service Azure qui connecte vos réseaux locaux via le service ExpressRoute à travers du réseau global de Microsoft. Par exemple, si vous disposez d’un centre de données privé en Californie, connecté à ExpressRoute dans la Silicon Valley, et d’un autre centre de données privé au Texas, connecté à ExpressRoute à Dallas, ExpressRoute Global Reach vous permet de connecter vos différents centres de données privés par le biais de deux circuits ExpressRoute, et le trafic entre vos centres de données traversera le réseau principal de Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Comment faire pour activer ou désactiver ExpressRoute Global Reach ?

Vous activez ExpressRoute Global Reach en connectant vos circuits ExpressRoute ensemble. Vous désactivez la fonctionnalité en déconnectant les circuits. Reportez-vous à la configuration.

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>ExpressRoute Premium est-il nécessaire pour ExpressRoute Global Reach ?

Si vos circuits ExpressRoute se trouvent dans la même région géopolitique, vous n’avez pas besoin d’ExpressRoute Premium pour les connecter entre eux. Si deux circuits ExpressRoute sont dans des régions géopolitiques différentes, vous avez besoin d’ExpressRoute Premium pour les deux circuits, afin de pouvoir les connecter entre eux. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Comment est facturée la fonctionnalité ExpressRoute Global Reach ?

ExpressRoute permet d’établir une connectivité entre votre réseau local et des services de cloud computing Microsoft. ExpressRoute Global Reach permet d’établir une connectivité entre vos réseaux locaux via vos circuits ExpressRoute existants, en utilisant le réseau global de Microsoft. La fonctionnalité ExpressRoute Global Reach est facturée séparément du service ExpressRoute existant. Des frais de module complémentaire sont associés à l’activation de cette fonctionnalité sur chaque circuit ExpressRoute. Le trafic entre vos réseaux locaux activés par ExpressRoute Global Reach est facturé selon un tarif de sortie au niveau de la source et selon un tarif d’entrée au niveau de la destination. Les tarifs sont basés sur la zone dans laquelle se trouvent les circuits. Consultez <pricing page>

### <a name="where-is-expressroute-global-reach-supported"></a>Dans quelles régions la fonctionnalité ExpressRoute Global Reach est-elle prise en charge ?

La fonctionnalité ExpressRoute Global Reach est prise en charge dans les pays suivants. Les circuits ExpressRoute doivent être créés sur les emplacements de peering dans ces pays.

* Australie
* Hong Kong (R.A.S.)
* Irlande
* Japon
* Pays-bas
* Royaume-Uni
* États-Unis

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>J’ai plus de deux réseaux locaux, chacun connecté à un circuit ExpressRoute. Puis-je activer ExpressRoute Global Reach pour connecter tous mes réseaux locaux ensemble ?

Oui, sous réserve que les circuits se trouvent dans les pays pris en charge. Vous devez connecter deux circuits ExpressRoute à la fois. Pour créer un réseau entièrement maillé, vous devez énumérer toutes les paires de circuit et répéter la configuration. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Puis-je activer la fonctionnalité ExpressRoute Global Reach entre deux circuits ExpressRoute se trouvant au même emplacement de peering ?

Non. Les deux circuits doivent provenir d’emplacements de peering différents. Si un métro dans un pays pris en charge a plusieurs emplacements de peering ExpressRoute, vous pouvez connecter ensemble les circuits ExpressRoute créés à différents emplacements de peering dans ce métro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Si la fonctionnalité ExpressRoute Global Reach est activée entre le circuit X et le circuit Y, mes réseaux locaux connectés au circuit X et au circuit Z communiquent-ils entre eux via le réseau de Microsoft ?

Non. Pour activer la connectivité entre deux de vos réseaux locaux, vous devez connecter explicitement les circuits ExpressRoute correspondants. Dans l’exemple ci-dessus, vous devez connecter le circuit X et le circuit Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>À quel débit réseau puis-je m’attendre entre mes réseaux locaux après avoir activé la fonctionnalité ExpressRoute Global Reach ?

Le débit réseau entre vos réseaux locaux, activés par ExpressRoute Global Reach, est limité par le plus petit des deux circuits ExpressRoute.
