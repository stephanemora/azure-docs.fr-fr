---
title: 'Interopérabilité d’ExpressRoute, du réseau privé virtuel de site à site et de VNet Peering - Analyse du plan de données : interopérabilité des fonctionnalités de connectivité du serveur principal Azure | Microsoft Docs'
description: Cette page fournit l’analyse du plan de données de la configuration de test qui est créée pour analyser l’interopérabilité des fonctionnalités d’ExpressRoute, du réseau privé virtuel de site à site et de VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947002"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>Interopérabilité d’ExpressRoute, du réseau privé virtuel de site à site et de VNet Peering - Analyse du plan de données

Dans cet article, nous allons passer en revue l’analyse du plan de données de l’initialisation (tearDown) de test. Pour passer en revue l’initialisation (tearDown) de test, consultez l’article relatif à [l’initialisation (tearDown) de test][Setup]. Pour passer en revue les détails de la configuration de l’initialisation (tearDown) de test, consultez l’article relatif à la [configuration de l’initialisation (tearDown) de test][Configuration]. Pour passer en revue l’analyse du plan de contrôle de l’initialisation (tearDown) de test, consultez l’article relatif à [l’analyse du plan de contrôle][Control-Analysis].

L’analyse du plan de données examine le chemin d’accès emprunté par les paquets allant d’un réseau local (LAN/réseau virtuel) à l’autre au sein d’une topologie. Le chemin d’accès aux données entre deux réseaux locaux peut ne pas être nécessairement symétrique. Dans cet article, nous allons donc analyser le chemin d’accès de transfert entre deux réseaux locaux séparément du chemin d’accès inverse.

##<a name="data-path-from-hub-vnet"></a>Chemin d’accès aux données à partir du réseau virtuel hub

###<a name="path-to-spoke-vnet"></a>Chemin d’accès au réseau virtuel spoke

VNet Peering émule les fonctionnalités de pont réseau entre les deux réseaux virtuels qui sont appairés. La sortie de détermination d’itinéraire d’un réseau virtuel hub vers une machine virtuelle dans le réseau virtuel spoke est indiquée ci-dessous :

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

La capture d’écran suivante illustre la vue graphique de la connexion entre le réseau virtuel hub et le réseau virtuel spoke présentée par Azure Network Watcher :


[![1]][1]

###<a name="path-to-branch-vnet"></a>Chemin d’accès au réseau virtuel branch

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon est la passerelle VPN du réseau virtuel hub. Le deuxième tronçon est la passerelle VPN du réseau virtuel branch, dont l’adresse IP n’est pas publiée dans le réseau virtuel hub. Le troisième tronçon est la machine virtuelle sur le réseau virtuel branch.

La capture d’écran suivante illustre la vue graphique de la connexion entre le réseau virtuel hub et le réseau virtuel branch présentée par Azure Network Watcher :

[![2]][2]

Pour la même connexion, la capture d’écran suivante est le mode Grille présenté par Azure Network Watcher :

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>Chemin d’accès à l’emplacement local 1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon va du point de terminaison du tunnel ExpressRoute à MSEE. Le deuxième et le troisième tronçons sont respectivement les adresses IP du routeur CE et du réseau local à l’emplacement local 1. Ces adresses IP ne sont pas publiées dans le réseau virtuel hub. Le quatrième tronçon est la machine virtuelle sur l’emplacement local 1.


###<a name="path-to-on-premises-location-2"></a>Chemin d’accès à l’emplacement local 2

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon va du point de terminaison du tunnel ExpressRoute à MSEE. Le deuxième et le troisième tronçons sont respectivement les adresses IP du routeur CE et du réseau local à l’emplacement local 2. Ces adresses IP ne sont pas publiées dans le réseau virtuel hub. Le quatrième tronçon est la machine virtuelle sur l’emplacement local 2.

###<a name="path-to-remote-vnet"></a>Chemin d’accès au réseau virtuel distant

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon va du point de terminaison du tunnel ExpressRoute à MSEE. Le deuxième tronçon est l’adresse IP de passerelle du réseau virtuel distant. La plage d’adresses IP du deuxième tronçon n’est pas publiée dans le réseau virtuel hub. Le troisième tronçon est la machine virtuelle sur le réseau virtuel distant.

##<a name="data-path-from-spoke-vnet"></a>Chemin d’accès aux données à partir du réseau virtuel spoke

Rappelez que le réseau virtuel spoke partage la vue du réseau virtuel hub. Via VNet Peering, le réseau virtuel spoke utilise la connectivité de la passerelle distante du réseau virtuel hub comme si sa connexion avec le réseau virtuel spoke était directe.

###<a name="path-to-hub-vnet"></a>Chemin d’accès au réseau virtuel hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>Chemin d’accès au réseau virtuel branch

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon est la passerelle VPN du réseau virtuel hub. Le deuxième tronçon est la passerelle VPN du réseau virtuel branch, dont l’adresse IP n’est pas publiée dans le réseau virtuel hub/spoke. Le troisième tronçon est la machine virtuelle sur le réseau virtuel branch.

###<a name="path-to-on-premises-location-1"></a>Chemin d’accès à l’emplacement local 1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon va du point de terminaison du tunnel ExpressRoute du réseau virtuel à MSEE. Le deuxième et le troisième tronçons sont respectivement les adresses IP du routeur CE et du réseau local à l’emplacement local 1. Ces adresses IP ne sont pas publiées dans le réseau virtuel hub/spoke. Le quatrième tronçon est la machine virtuelle sur l’emplacement local 1.

###<a name="path-to-on-premises-location-2"></a>Chemin d’accès à l’emplacement local 2

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon va du point de terminaison du tunnel ExpressRoute du réseau virtuel à MSEE. Le deuxième et le troisième tronçons sont respectivement les adresses IP du routeur CE et du réseau local à l’emplacement local 2. Ces adresses IP ne sont pas publiées dans les réseaux virtuels hub/spoke. Le quatrième tronçon est la machine virtuelle sur l’emplacement local 2.

###<a name="path-to-remote-vnet"></a>Chemin d’accès au réseau virtuel distant

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon va du point de terminaison du tunnel ExpressRoute du réseau virtuel à MSEE. Le deuxième tronçon est l’adresse IP de passerelle du réseau virtuel distant. La plage d’adresses IP du deuxième tronçon n’est pas publiée dans le réseau virtuel hub/spoke. Le troisième tronçon est la machine virtuelle sur le réseau virtuel distant.

##<a name="data-path-from-branch-vnet"></a>Chemin d’accès aux données à partir du réseau virtuel branch

###<a name="path-to-hub-vnet"></a>Chemin d’accès au réseau virtuel hub

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon est la passerelle VPN du réseau virtuel branch. Le deuxième tronçon est la passerelle VPN du réseau virtuel hub, dont l’adresse IP n’est pas publiée dans le réseau virtuel distant. Le troisième tronçon est la machine virtuelle sur le réseau virtuel hub.

###<a name="path-to-spoke-vnet"></a>Chemin d’accès au réseau virtuel spoke

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon est la passerelle VPN du réseau virtuel branch. Le deuxième tronçon est la passerelle VPN du réseau virtuel hub, dont l’adresse IP n’est pas publiée dans le réseau virtuel distant. Le troisième tronçon est la machine virtuelle sur le réseau virtuel spoke.

###<a name="path-to-on-premises-location-1"></a>Chemin d’accès à l’emplacement local 1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, le premier tronçon est la passerelle VPN du réseau virtuel branch. Le deuxième tronçon est la passerelle VPN du réseau virtuel hub, dont l’adresse IP n’est pas publiée dans le réseau virtuel distant. Le troisième tronçon est le point de terminaison du tunnel VPN sur le routeur CE principal. Le quatrième tronçon est une adresse IP interne de l’adresse IP de réseau local à l’emplacement local 1 qui n’est pas publiée en dehors du routeur CE. Le cinquième tronçon est la machine virtuelle de destination sur l’emplacement local 1.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>Chemin d’accès à l’emplacement local 2 et au réseau virtuel distant

Comme nous l’avons indiqué auparavant dans l’analyse du plan de contrôle, le réseau virtuel branch n’a aucune visibilité sur l’emplacement local 2 ni sur le réseau virtuel distant conformément à la configuration réseau. Les résultats suivants du test ping confirment le fait. 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

##<a name="data-path-from-on-premises-location-1"></a>Chemin d’accès aux données à partir de l’emplacement local 1

###<a name="path-to-hub-vnet"></a>Chemin d’accès au réseau virtuel hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

Dans la détermination d’itinéraire ci-dessus, les deux premiers tronçons font partie du réseau local. Le troisième tronçon est l’interface MSEE principale accessible sur le routeur CE. Le quatrième tronçon est la passerelle ExpressRoute du réseau virtuel hub, dont la plage d’adresses IP n’est pas publiée sur le réseau local. Le cinquième tronçon est la machine virtuelle de destination.

Azure Network Watcher fournit uniquement une vue centrée sur Azure. Par conséquent, pour la vue centrée en local, nous avons utilisé Azure Network Performance Monitor (NPM). NPM fournit des agents qui peuvent être des serveurs installés sur le réseau en dehors d’Azure et qui effectuent une analyse du chemin d’accès aux données.

La capture d’écran suivante est une vue de la topologie de la connectivité de la machine virtuelle de l’emplacement local 1 à la machine virtuelle du réseau virtuel hub via ExpressRoute.

[![4]][4]

Rappelez-vous que l’initialisation (tearDown) de test utilise le réseau privé virtuel de site à site comme connectivité de secours pour ExpressRoute entre l’emplacement local 1 et le réseau virtuel hub. Pour tester le chemin d’accès aux données de retour, nous allons provoquer un échec de la liaison ExpressRoute entre le routeur CE principal de l’emplacement local 1 et le composant MSEE correspondant en arrêtant l’interface CE accessible sur le MSEE.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

La capture d’écran suivante est une vue de la topologie de la connectivité de la machine virtuelle de l’emplacement local 1 à la machine virtuelle du réseau virtuel hub via la connectivité du réseau privé virtuel de site à site lorsque la connectivité ExpressRoute est à l’arrêt.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>Chemin d’accès au réseau virtuel spoke

Rétablissons la connectivité principale ExpressRoute pour effectuer l’analyse du chemin d’accès aux données vers le réseau virtuel spoke.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Établissons la connectivité ExpressRoute 1 principale pour le reste de l’analyse du chemin d’accès aux données.

###<a name="path-to-branch-vnet"></a>Chemin d’accès au réseau virtuel branch

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>Chemin d’accès à l’emplacement local 2

Comme nous l’avons indiqué auparavant dans l’analyse du plan de contrôle, l’emplacement local 1 n’a aucune visibilité sur l’emplacement local 2 conformément à la configuration réseau. Les résultats suivants du test ping confirment le fait. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>Chemin d’accès au réseau virtuel distant

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>Chemin d’accès aux données à partir de l’emplacement local 2

###<a name="path-to-hub-vnet"></a>Chemin d’accès au réseau virtuel hub

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Chemin d’accès au réseau virtuel spoke

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>Chemin d’accès au réseau virtuel branch, à l’emplacement local 1 et au réseau virtuel distant

Comme nous l’avons indiqué auparavant dans l’analyse du plan de contrôle, l’emplacement local 1 n’a aucune visibilité sur le réseau virtuel branch, l’emplacement local 1 et le réseau virtuel distant conformément à la configuration réseau. 

##<a name="data-path-from-remote-vnet"></a>Chemin d’accès aux données à partir du réseau virtuel distant

###<a name="path-to-hub-vnet"></a>Chemin d’accès au réseau virtuel hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Chemin d’accès au réseau virtuel spoke

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>Chemin d’accès au réseau virtuel branch et à l’emplacement local 2

Comme nous l’avons indiqué auparavant dans l’analyse du plan de contrôle, le réseau virtuel distant n’a aucune visibilité sur le réseau virtuel branch ni sur l’emplacement local 2 conformément à la configuration réseau. 


### <a name="path-to-on-premises-location-1"></a>Chemin d’accès à l’emplacement local 1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>Pour aller plus loin

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Utilisation de la connectivité ExpressRoute et du réseau privé virtuel de site à site en tandem

####<a name="site-to-site-vpn-over-expressroute"></a>Réseau privé virtuel de site à site via ExpressRoute

Un réseau privé virtuel de site à site peut être configuré via l’appairage Microsoft ExpressRoute pour échanger en privé des données entre votre réseau local et vos réseaux virtuels Azure en assurant confidentialité, anti-relecture, authenticité et intégrité des données. Pour plus d’informations sur la configuration du réseau privé virtuel IPSec de site à site en mode tunnel via l’appairage Microsoft ExpressRoute, consultez l’article [Configurer un réseau VPN de site à site via l’appairage Microsoft ExpressRoute][S2S-Over-ExR]. 

La principale restriction de la configuration d’un réseau privé virtuel de site à site via l’homologation Microsoft relève du débit. Le débit via le tunnel IPSec est limité par la capacité de la passerelle du réseau privé virtuel. Le débit de la passerelle du réseau privé virtuel est inférieur au débit ExpressRoute. Dans de tels scénarios, l’utilisation du tunnel IPSec pour garantir un trafic sécurisé élevé et l’appairage privé pour tout autre trafic permet d’optimiser l’utilisation de la bande passante ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Réseau privé virtuel de site à site comme chemin d’accès de basculement sécurisé pour ExpressRoute
ExpressRoute est proposé comme une paire de circuits redondants pour garantir une haute disponibilité. Vous pouvez configurer une connectivité ExpressRoute géo-redondante dans différentes régions Azure. En outre, comme nous l’avons fait dans notre initialisation (tearDown) de test, dans une région Azure donnée, si vous avez besoin d’un chemin d’accès de basculement pour votre connectivité ExpressRoute, vous pouvez l’obtenir via le réseau VPN de site à site. Lorsque les mêmes préfixes sont publiés sur ExpressRoute et le réseau VPN de site à site, Azure choisit ExpressRoute plutôt que le réseau VPN de site à site. Pour éviter un routage asymétrique entre ExpressRoute et le réseau privé virtuel de site à site, la configuration du réseau local doit également en faire autant en préférant ExpressRoute à la connectivité du réseau privé virtuel de site à site.

Pour plus d’informations sur la configuration des connexions coexistantes d’ExpressRoute et du réseau privé virtuel de site à site, consultez l’article [Configurer des connexions coexistantes d’ExpressRoute et de site à site en utilisant PowerShell][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Extension de la connectivité principale aux emplacements branch et de réseaux virtuels spoke

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connectivité de réseau virtuel spoke via VNet Peering

L’architecture de réseau virtuel hub-and-spoke est largement utilisée. Le hub est un réseau virtuel dans Azure qui centralise la connectivité entre vos réseaux virtuels spoke et votre réseau local. Les spokes sont des réseaux virtuels appairés avec le hub et qui peuvent être utilisés pour isoler les charges de travail. Le trafic circule entre le centre de données local et le hub via une connexion ExpressRoute ou VPN. Pour plus d’informations sur l’architecture, consultez l’article [Implémenter une topologie de réseau hub-and-spoke dans Azure][Hub-n-Spoke].

Le service VNet Peering dans une région autorise les réseaux virtuels spoke à utiliser les passerelles de réseau virtuel hub (passerelles VPN et ExpressRoute) pour communiquer avec les réseaux distants.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Connectivité de réseau virtuel branch à l’aide du réseau VPN de site à site

Si vous souhaitez que les réseaux virtuels branch (dans différentes régions) et les réseaux locaux communiquent entre eux via un réseau virtuel hub, la solution Azure native est la connectivité VPN de site à site à l’aide du VPN. Vous pouvez également utiliser une appliance virtuelle réseau pour le routage dans le hub.

Pour configurer des passerelles VPN, consultez l’article [Configuration d’une passerelle VPN][VPN]. Pour déployer des appliances virtuelles réseau hautement disponibles, consultez l’article [Déployer des appliances virtuelles réseau hautement disponibles][Deploy-NVA].

## <a name="next-steps"></a>Étapes suivantes

Pour connaître le nombre de circuits ExpressRoute que vous pouvez connecter à une passerelle ExpressRoute ou le nombre de passerelles ExpressRoute que vous pouvez connecter à un circuit ExpressRoute ou pour découvrir d’autres limites de mise à l’échelle d’ExpressRoute, consultez l’article [Forum Aux Questions ExpressRoute][ExR-FAQ].


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Vue Network Watcher de la connectivité entre le réseau virtuel hub et le réseau virtuel spoke"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Vue Network Watcher de la connectivité entre le réseau virtuel hub et le réseau virtuel branch"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Mode Grille de Network Watcher de la connectivité entre le réseau virtuel hub et le réseau virtuel branch"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Vue Network Performance Monitor de la connectivité entre la machine virtuelle de l’emplacement 1 et le réseau virtuel hub via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Vue Network Performance Monitor de la connectivité entre la machine virtuelle de l’emplacement 1 et le réseau virtuel hub via le réseau privé virtuel de site à site"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




