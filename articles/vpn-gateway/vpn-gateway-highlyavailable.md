---
title: À propos des configurations de passerelle hautement disponibles
titleSuffix: Azure VPN Gateway
description: Découvrez les options de configuration hautement disponibles avec des passerelles VPN Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/27/2021
ms.author: yushwang
ms.openlocfilehash: f00de78ba4e2b46595489f46155546157642fb12
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953852"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Connectivité hautement disponible entre différents locaux et de réseau virtuel à réseau virtuel

Cet article fournit une vue d’ensemble des options de configuration haute disponibilité dont vous pouvez tirer parti pour la connectivité entre vos réseaux locaux et la connectivité entre deux réseaux virtuels en utilisant des passerelles VPN Azure.

## <a name="about-vpn-gateway-redundancy"></a><a name = "activestandby"></a>À propos de la redondance de passerelles VPN

Chaque passerelle VPN Azure comprend deux instances dans une configuration de type actif / passif. En cas de maintenance planifiée ou d’interruption non planifiée au niveau de l’instance active, l’instance de secours prend automatiquement le relais (par un basculement) et reprend les connexions VPN S2S ou entre deux réseaux virtuels. Le basculement entraîne une brève interruption. Dans le cadre d’une maintenance planifiée, la connectivité doit être restaurée dans les 10 à 15 secondes. En cas de problèmes non planifiés, la récupération de la connexion est plus longue et peut atteindre de 1 à 3 minutes dans le pire des cas. Pour les connexions client VPN P2S à la passerelle, les connexions P2S seront rompues et les utilisateurs devront se reconnecter à partir des ordinateurs clients.

:::image type="content" source="./media/vpn-gateway-highlyavailable/active-standby.png" alt-text="Diagramme montrant un site local avec des sous-réseaux IP privés et un VPN local connecté à une passerelle VPN Azure active pour se connecter aux sous-réseaux hébergés dans Azure, avec une passerelle de secours disponible." border="false":::

## <a name="highly-available-cross-premises"></a>Connectivité hautement disponible entre différents locaux

Plusieurs options sont disponibles pour augmenter la disponibilité d’une connexion entre différents locaux :

* Utilisation de plusieurs périphériques VPN en local
* Utilisation d’une passerelle VPN Azure en mode actif-actif
* Combinaison des deux

### <a name="multiple-on-premises-vpn-devices"></a><a name = "activeactiveonprem"></a>Utilisation de plusieurs périphériques VPN en local

Vous pouvez utiliser plusieurs périphériques VPN de votre réseau local pour vous connecter à votre passerelle VPN Azure, comme illustré dans le schéma suivant :

:::image type="content" source="./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png" alt-text="Diagramme montrant plusieurs sites locaux comportant des sous-réseaux IP privés et un VPN local connecté à une passerelle VPN Azure active pour se connecter aux sous-réseaux hébergés dans Azure, avec une passerelle de secours disponible." border="false":::

Cette configuration offre plusieurs tunnels actifs reliant la même passerelle VPN Azure à vos périphériques locaux dans le même emplacement. Elle comporte certaines exigences et contraintes :

1. Vous devez créer plusieurs connexions VPN S2S entre vos périphériques VPN et Azure. Lorsque vous connectez plusieurs périphériques VPN du même réseau local vers Azure, vous devez créer une passerelle réseau locale pour chaque périphérique VPN et une connexion entre votre passerelle VPN Azure et chaque passerelle réseau locale.
1. Les passerelles réseau locales correspondant à vos périphériques VPN doivent avoir des adresses IP publiques uniques dans la propriété « GatewayIpAddress ».
1. Cette configuration requiert le protocole BGP. Pour chaque passerelle réseau locale qui représente un périphérique VPN, une adresse IP d’homologue BGP unique doit être spécifiée dans la propriété « BgpPeerIpAddress ».
1. Vous devez utiliser le protocole BGP pour annoncer les mêmes préfixes que les préfixes de réseau local sur votre passerelle VPN Azure. Le trafic sera alors transmis simultanément via ces tunnels.
1. Vous devez utiliser un routage ECMP (Equal-Cost Multi-Path).
1. Chaque connexion est comptabilisée par rapport au nombre maximal de tunnels pour votre passerelle VPN Azure, soit 10 pour les références SKU De base et Standard, et 30 pour les références SKU Hautes performances. 

Dans cette configuration, la passerelle VPN Azure est toujours en mode actif-passif, ce qui signifie que l’on aura toujours le même comportement de basculement accompagné d’une brève interruption comme décrit [ci-dessus](#activestandby). Mais cette configuration évite les défaillances ou les interruptions sur votre réseau local et sur vos périphériques VPN.

### <a name="active-active-vpn-gateways"></a>Passerelles VPN en mode actif/actif

Il est possible de créer une passerelle VPN Azure dans une configuration actif/actif. Dans cette configuration, les deux instances de machines virtuelles passerelles établissent des tunnels S2S VPN vers le périphérique VPN local, comme le montre le schéma suivant :

:::image type="content" source="./media/vpn-gateway-highlyavailable/active-active.png" alt-text="Diagramme montrant un site local avec des sous-réseaux IP privés et un VPN local connecté à deux passerelles VPN Azure actives pour se connecter aux sous-réseaux hébergés dans Azure." border="false":::

Dans cette configuration, chaque instance de passerelle Azure aura une adresse IP publique unique et chacune va établir un tunnel VPN S2S IPsec/IKE sur votre périphérique VPN local spécifié dans votre passerelle et votre connexion de réseau local. Notez que les deux tunnels VPN font en fait partie de la même connexion. Vous devez toujours configurer votre périphérique VPN local pour accepter ou établir deux tunnels VPN S2S sur ces deux adresses IP publiques de passerelle VPN Azure.

Étant donné que les instances de passerelle Azure sont en configuration actif-actif, le trafic qui transite entre votre réseau virtuel Azure et votre réseau local sera routé simultanément via les deux tunnels, même si votre périphérique VPN local peut privilégier un tunnel plutôt qu’un autre. Dans le cas d’un seul canal TCP ou UDP, Azure tente d’utiliser le même tunnel lors de l’envoi de paquets au réseau local. Toutefois, il est possible que ce dernier utilise un autre tunnel pour envoyer des paquets à Azure.

Lorsqu’une instance de passerelle est affectée par une maintenance planifiée ou un événement imprévu, le tunnel IPsec entre votre périphérique VPN local et cette instance est déconnecté. Les itinéraires correspondants sur vos périphériques VPN doivent être supprimés ou retirés automatiquement afin de permettre le basculement du trafic sur l’autre tunnel IPsec actif. Côté Azure, le basculement se produit automatiquement de l’instance affectée vers l’instance active.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Double redondance : passerelles VPN de type actif-actif pour Azure et les réseaux locaux

L’option la plus fiable consiste à combiner les passerelles de type actif-actif sur votre réseau et sur Azure, comme illustré dans le schéma ci-dessous.

:::image type="content" source="./media/vpn-gateway-highlyavailable/dual-redundancy.png" alt-text="Diagramme illustrant un scénario de double redondance." border="false":::

Il s’agit ici de créer et de configurer la passerelle VPN Azure dans une configuration actif/actif et de créer deux passerelles réseau locales et deux connexions pour les deux périphériques VPN locaux décrits ci-dessus. Vous obtenez une connectivité entièrement maillée pour les 4 tunnels IPsec qui relient votre réseau virtuel Azure à votre réseau local.

L’ensemble des passerelles et des tunnels étant actifs côté Azure, le trafic est réparti entre les 4 tunnels simultanément, même si chaque flux TCP ou UDP suivra à nouveau le même tunnel ou le même chemin côté Azure. Même en répartissant le trafic, vous pouvez constater un débit légèrement supérieur sur les tunnels IPsec, cette configuration étant essentiellement axée sur la haute disponibilité. En raison de la nature statistique de la propagation, il est difficile de déterminer de quelle manière les différentes conditions de trafic d’applications affecteront le débit global.

Cette topologie suppose de faire appel à deux passerelles réseau locales et à deux connexions pour pouvoir prendre en charge les deux périphériques VPN locaux. Elle requiert également le protocole BGP pour autoriser les deux connexions au même réseau local. Ces exigences sont identiques à celles décrites [ci-dessus](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet"></a>Connectivité hautement disponible de réseau virtuel à réseau virtuel 

La même configuration actif-actif peut également s’appliquer aux connexions entre deux réseaux virtuels Azure. Vous pouvez créer des passerelles VPN actif-actif pour les deux réseaux virtuels et les connecter ensemble pour obtenir la même connectivité entièrement maillée pour les 4 tunnels placés entre les deux réseaux virtuels, comme illustré dans le schéma ci-dessous :

:::image type="content" source="./media/vpn-gateway-highlyavailable/vnet-to-vnet.png" alt-text="Diagramme montrant deux régions Azure hébergeant des sous-réseaux IP privés et deux passerelles VPN Azure via lesquelles les deux sites virtuels se connectent." border="false":::

Ainsi, il existe toujours une paire de tunnels entre les deux réseaux virtuels pour tout événement de maintenance planifiée, ce qui garantit une meilleure disponibilité. Bien que la même topologie requiert deux connexions pour permettre une connectivité entre les réseaux locaux, la topologie entre deux réseaux virtuels décrite ci-dessus ne nécessite qu’une seule connexion pour chaque passerelle. En outre, l’utilisation du protocole BGP n’est nécessaire que si le transit doit être routé via la connexion entre les deux réseaux virtuels.

## <a name="next-steps"></a>Étapes suivantes
Pour connaître les étapes de configuration des connexions en mode actif-actif entre des réseaux locaux ou entre deux réseaux virtuels, consultez la page [Configuring Active-Active VPN Gateways for Cross-Premises and VNet-to-VNet Connections](vpn-gateway-activeactive-rm-powershell.md) (Configuration des passerelles VPN actif-actif pour des connexions entre des réseaux locaux et entre deux réseaux virtuels).

