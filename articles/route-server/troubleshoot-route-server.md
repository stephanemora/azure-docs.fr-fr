---
title: Résoudre les problèmes liés à Azure Route Server
description: Découvrez comment résoudre les problèmes liés à Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 09/23/2021
ms.author: duau
ms.openlocfilehash: abece146cb2394046b7f46aa96ea70dc124cade0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649054"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Résolution des problèmes liés à Azure Route Server

## <a name="connectivity-issues"></a>Problèmes de connectivité

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Pourquoi ma NVA rencontre-t-elle des problèmes de connectivité après avoir publié l’itinéraire par défaut (0.0.0.0/0) sur Azure Route Server ?
Lorsque votre NVA publie l’itinéraire par défaut, Azure Route Server la programme pour toutes les machines virtuelles du réseau virtuel, y compris la NVA elle-même. Cet itinéraire par défaut définit la NVA comme tronçon suivant pour tout le trafic Internet. Si votre NVA a besoin d’une connexion Internet, vous devez configurer un [itinéraire défini par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md) pour remplacer l’itinéraire par défaut de la NVA et joindre l’UDR au sous-réseau sur lequel la NVA est hébergée (voir l’exemple ci-dessous). Sinon, l’ordinateur hôte de la NVA continue d’envoyer le trafic Internet, y compris celui envoyé par la NVA à la NVA.

| Routage | Tronçon suivant |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-or-why-is-the-bgp-peering-flapping"></a>Pourquoi puis-je effectuer un test ping depuis ma NVA vers l’adresse IP de pair BGP sur Azure Route Server, alors qu’après avoir configuré le peering BGP entre eux, je ne peux plus effectuer de test ping vers la même adresse IP ? Ou : pourquoi le peering BGP bagotte-t-il ?

Dans certaines NVA, vous devez ajouter une route statique pour le sous-réseau d’Azure Route Server. Par exemple, si Azure Route Server est dans la plage 10.0.255.0/27 et que votre NVA se trouve dans la plage 10.0.1.0/24, vous devez ajouter la route suivante à la table de routage dans la NVA :

| Routage | Tronçon suivant |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 est l’adresse IP de passerelle par défaut dans le sous-réseau où votre NVA (ou plus précisément, une des cartes réseau) est hébergée.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>Pourquoi est-ce que je perds la connectivité à mon réseau local via ExpressRoute et/ou VPN Azure lorsque je déploie Azure Route Server sur un réseau virtuel qui possède déjà la passerelle ExpressRoute et/ou la passerelle VPN Azure ?
Lorsque vous déployez Azure Route Server sur un réseau virtuel, nous devons mettre à jour le plan de contrôle entre les passerelles et le réseau virtuel. Pendant cette mise à jour, les machines virtuelles du réseau virtuel perdront temporairement leur connectivité au réseau local. Nous vous recommandons vivement de planifier une maintenance pour déployer Azure Route Server dans votre environnement de production.  

## <a name="control-plane-issues"></a>Problèmes liés au plan de contrôle

### <a name="why-does-my-on-premises-network-connected-to-azure-vpn-gateway-not-receive-the-default-route-advertised-by-azure-route-server"></a>Pourquoi mon réseau local connecté à une passerelle VPN Azure ne reçoit-il pas l’itinéraire par défaut publié par le Serveur de routes Azure ?

Bien que la passerelle VPN Azure puisse recevoir l’itinéraire par défaut à partir de ses homologues BGP, dont le Serveur de routes Azure, elle [ne publie pas l’itinéraire par défaut](../vpn-gateway/vpn-gateway-vpn-faq.md#what-address-prefixes-will-azure-vpn-gateways-advertise-to-me) vers d’autres homologues. 

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Pourquoi ma NVA ne reçoit-elle pas de routes d’Azure Route Server même si le peering BGP est opérationnel ?

Le numéro ASN utilisé par Azure Route Server est 65515. Veillez à configurer un autre numéro ASN pour votre NVA afin qu’une session « eBGP » puisse être établie entre votre NVA et Azure Route Server et que la propagation des routes puisse se produire automatiquement. Veillez à activer « multi-hop » dans votre configuration BGP, car votre NVA et Azure Route Server se trouvent dans des sous-réseaux différents du réseau virtuel.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>Le peering BGP entre ma NVA et Azure Route Server est opérationnel. Je peux voir des routes correctement échangées entre eux. Pourquoi les routes de la NVA ne sont-elles pas dans la table de routage effective de ma machine virtuelle ? 

* Si votre machine virtuelle se trouve dans le même réseau virtuel que votre NVA et Azure Route Server :

     Azure Route Server expose deux adresses IP de pair BGP, qui sont hébergées sur deux machines virtuelles partageant la responsabilité de l’envoi des routes à toutes les autres machines virtuelles exécutées sur votre réseau virtuel. Chacune de vos NVA doit configurer deux sessions BGP identiques (par exemple, utilisez le même numéro AS, le même chemin AS et publiez le même ensemble de routes) sur les deux machines virtuelles afin que vos machines virtuelles sur le réseau virtuel puissent recevoir des informations de routage cohérentes à partir d’Azure Route Server. Reportez-vous au diagramme ci-dessous.

    ![Diagramme montrant une appliance virtuelle réseau avec Route Server.](./media/faq/network-virtual-appliances.png)

    Si vous avez deux instances de la NVA ou plus, vous *pouvez* publier différents chemins AS pour la même route à partir de différentes instances d’appliance virtuelle réseau si vous souhaitez désigner une instance de NVA comme étant active et une autre comme étant passive.

* Si votre machine virtuelle se trouve sur un autre réseau virtuel que celui qui héberge votre NVA et Azure Route Server. Vérifiez si le peering de réseaux virtuels est activé entre les deux réseaux virtuels *et* si l’option Utiliser le serveur de routage distant est activée sur le réseau virtuel de votre machine virtuelle.

### <a name="why-is-the-equal-cost-multi-path-ecmp-function-of-my-expressroute-turned-off-after-i-deploy-azure-route-server-to-the-virtual-network"></a>Pourquoi la fonction ECMP (Equal-Cost Multi-Path) de mon ExpressRoute est-elle désactivée après le déploiement du Serveur de routes Azure vers le réseau virtuel ?

Lorsque vous publiez les mêmes itinéraires de votre réseau local vers Azure sur plusieurs connexions ExpressRoute, normalement, ECMP est activé par défaut pour le trafic destiné à ces itinéraires d’Azure vers votre réseau local. Toutefois, une fois le serveur de routes déployé, les informations de chemins d’accès multiples sont perdues dans l’échange via BGP entre ExpressRoute et le Serveur de routes Azure, de sorte que le trafic à partir d’Azure traverse uniquement l’une des connexions ExpressRoute. Cette limitation sera levée dans la future version du Serveur de routes Azure.  

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
