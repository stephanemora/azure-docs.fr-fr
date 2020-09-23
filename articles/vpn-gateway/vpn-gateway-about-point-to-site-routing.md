---
title: 'Passerelle VPN Azure : à propos du routage P2S'
description: En savoir plus sur le routage VPN point à site Azure pour différents systèmes d’exploitation, protocoles d’accès à distance et configurations de réseau virtuel.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: a3603c7a4b704327f829588f0fc5827d97288be0
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440811"
---
# <a name="about-point-to-site-vpn-routing"></a>À propos du routage VPN point à site

Cet article vous aide à comprendre le comportement du routage VPN point à site Azure. Le comportement du routage VPN point à site (P2S) dépend du système d’exploitation client, du protocole utilisé pour la connexion VPN et du mode de connexion des réseaux virtuels entre eux.

Actuellement, Azure prend en charge deux protocoles pour l’accès à distance, IKEv2 et SSTP. IKEv2 est pris en charge sur de nombreux systèmes d’exploitation clients, notamment Windows, Linux, MacOS, Android et iOS. SSTP est pris en charge sur Windows uniquement. Si vous apportez un changement à la topologie de votre réseau et que vous avez des clients VPN Windows, vous devez retélécharger et réinstaller le package client VPN pour les clients Windows afin d’appliquer ce changement au client.

> [!NOTE]
> Cet article s’applique uniquement à IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>À propos des diagrammes

Vous trouverez différents diagrammes dans cet article. Chaque section présente une topologie ou une configuration différente. Dans le cadre de cet article, les connexions site à site (S2S) et réseau virtuel à réseau virtuel fonctionnent de la même façon parce que les deux sont des tunnels IPsec. Toutes les passerelles VPN de cet article sont basées sur le routage.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Un réseau virtuel isolé

La connexion de passerelle VPN point à site de cet exemple est pour un réseau virtuel qui n’est connecté ni appairé à aucun autre réseau virtuel (VNet1). Dans cet exemple, les clients peuvent accéder à VNet1.

![Routage d’un réseau virtuel isolé](./media/vpn-gateway-about-point-to-site-routing/1.jpg "Routage d’un réseau virtuel isolé")

### <a name="address-space"></a>Espace d’adressage

* VNet1 : 10.1.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16, 192.168.0.0/24

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1

* Les clients non-Windows peuvent accéder à VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Plusieurs réseaux virtuels appairés

Dans cet exemple, la connexion de passerelle VPN point à site est pour VNet1. VNet1 est appairé à VNet2. VNet2 est appairé à VNet3. VNet1 est appairé à VNet4. Il n’existe aucun peering direct entre VNet1 et VNet3. VNet1 a l’option « Autoriser le transit par passerelle » activée, tandis que VNet2 et VNet4 ont l’option « Utiliser des passerelles distantes » activée.

Les clients utilisant Windows peuvent accéder directement aux réseaux virtuels appairés, mais le client VPN doit être retéléchargé si des changements sont apportés au peering du réseau virtuel ou à la topologie du réseau. Les clients non-Windows peuvent accéder directement aux réseaux virtuels appairés. L’accès n’est pas transitif et est limité aux réseaux virtuels directement appairés uniquement.

![Plusieurs réseaux virtuels appairés](./media/vpn-gateway-about-point-to-site-routing/2.jpg "Plusieurs réseaux virtuels appairés")

### <a name="address-space"></a>Espace d’adressage :

* VNet1 : 10.1.0.0/16

* VNet2 : 10.2.0.0/16

* VNet3 : 10.3.0.0/16

* VNet4 : 10.4.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1, VNet2 et VNet4, mais le client VPN doit être retéléchargé pour que les changements de topologie prennent effet.

* Les clients non-Windows peuvent accéder à VNet1, VNet2 et VNet4.

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Plusieurs réseaux virtuels connectés avec un VPN S2S

Dans cet exemple, la connexion de passerelle VPN point à site est pour VNet1. VNet1 est connecté à VNet2 avec une connexion VPN site à site. VNet2 est connecté à VNet3 avec une connexion VPN site à site. Il n’existe aucun peering direct ni aucune connexion VPN site à site entre VNet1 et VNet3. Toutes les connexions site à site n’exécutent pas BGP pour le routage.

Les clients utilisant Windows ou un autre système d’exploitation pris en charge peuvent accéder à VNet1 uniquement. Pour accéder à d’autres réseaux virtuels, BGP doit être utilisé.

![Plusieurs réseaux virtuels et S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "Plusieurs réseaux virtuels et S2S")

### <a name="address-space"></a>Espace d’adressage

* VNet1 : 10.1.0.0/16

* VNet2 : 10.2.0.0/16

* VNet3 : 10.3.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16, 192.168.0.0/24

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1 uniquement

* Les clients non-Windows peuvent accéder à VNet1 uniquement

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Plusieurs réseaux virtuels connectés avec un VPN S2S (BGP)

Dans cet exemple, la connexion de passerelle VPN point à site est pour VNet1. VNet1 est connecté à VNet2 avec une connexion VPN site à site. VNet2 est connecté à VNet3 avec une connexion VPN site à site. Il n’existe aucun peering direct ni aucune connexion VPN site à site entre VNet1 et VNet3. Toutes les connexions site à site exécutent BGP pour le routage.

Les clients utilisant Windows ou un autre système d’exploitation pris en charge peuvent accéder à tous les réseaux virtuels qui sont connectés avec une connexion VPN site à site, mais les routes vers les réseaux virtuels connectés doivent être ajoutées manuellement dans les clients Windows.

![Plusieurs réseaux virtuels et S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "Plusieurs réseaux virtuels et S2S BGP")

### <a name="address-space"></a>Espace d’adressage

* VNet1 : 10.1.0.0/16

* VNet2 : 10.2.0.0/16

* VNet3 : 10.3.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1, VNet2 et VNet3, mais les routes vers VNet2 et VNet3 doivent être ajoutées manuellement.

* Les clients non-Windows peuvent accéder à VNet1, VNet2 et VNet3.

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Un réseau virtuel et une filiale

Dans cet exemple, la connexion de passerelle VPN point à site est pour VNet1. VNet1 n’est connecté / appairé à aucun autre réseau virtuel, mais est connecté à un site local via une connexion VPN site à site qui n’exécute pas BGP.

Les clients Windows et non-Windows peuvent uniquement accéder à VNet1.

![Routage avec un réseau virtuel et une filiale](./media/vpn-gateway-about-point-to-site-routing/5.jpg "Routage avec un réseau virtuel et une filiale")

### <a name="address-space"></a>Espace d’adressage

* VNet1 : 10.1.0.0/16

* Site1 : 10.101.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16, 192.168.0.0/24

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1 uniquement

* Les clients non-Windows peuvent accéder à VNet1 uniquement

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Un réseau virtuel et une filiale (BGP)

Dans cet exemple, la connexion de passerelle VPN point à site est pour VNet1. VNet1 n’est connecté ni appairé à aucun autre réseau virtuel, mais est connecté à un site local (Site1) via une connexion VPN site à site qui exécute BGP.

Les clients Windows peuvent accéder au réseau virtuel et à la filiale (Site1), mais les routes vers Site1 doivent être ajoutées manuellement dans le client. Les clients non-Windows peuvent accéder au réseau virtuel, ainsi qu’à la filiale locale.

![Un réseau virtuel et une filiale (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "Un réseau virtuel et une filiale")

### <a name="address-space"></a>Espace d’adressage

* VNet1 : 10.1.0.0/16

* Site1 : 10.101.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16, 192.168.0.0/24

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1 et à Site1, mais les routes vers Site1 doivent être ajoutées manuellement.

* Les clients non-Windows peuvent accéder à VNet1 et à Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Plusieurs réseaux virtuels connectés avec S2S et une filiale

Dans cet exemple, la connexion de passerelle VPN point à site est pour VNet1. VNet1 est connecté à VNet2 avec une connexion VPN site à site. VNet2 est connecté à VNet3 avec une connexion VPN site à site. Il n’existe aucun peering direct ni aucun tunnel VPN site à site entre les réseaux VNet1 et VNet3. VNet3 est connecté à une filiale (Site1) avec une connexion VPN site à site. Toutes les connexions VPN n’exécutent pas BGP.

Tous les clients peuvent accéder à VNet1 uniquement.

![S2S à plusieurs réseaux virtuels et filiale](./media/vpn-gateway-about-point-to-site-routing/7.jpg "S2S à plusieurs réseaux virtuels et filiale")

### <a name="address-space"></a>Espace d’adressage

* VNet1 : 10.1.0.0/16

* VNet2 : 10.2.0.0/16

* VNet3 : 10.3.0.0/16

* Site1 : 10.101.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16, 192.168.0.0/24

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1 uniquement

* Les clients non-Windows peuvent accéder à VNet1 uniquement

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Plusieurs réseaux virtuels connectés avec S2S et une filiale (BGP)

Dans cet exemple, la connexion de passerelle VPN point à site est pour VNet1. VNet1 est connecté à VNet2 avec une connexion VPN site à site. VNet2 est connecté à VNet3 avec une connexion VPN site à site. Il n’existe aucun peering direct ni aucun tunnel VPN site à site entre les réseaux VNet1 et VNet3. VNet3 est connecté à une filiale (Site1) avec une connexion VPN site à site. Toutes les connexions VPN exécutent BGP.

Les clients utilisant Windows peuvent accéder aux réseaux virtuels et aux sites qui sont connectés avec une connexion VPN site à site, mais les routes vers VNet2, VNet3 et Site1 doivent être ajoutées manuellement dans le client. Les clients non-Windows peuvent accéder aux réseaux virtuels et aux sites qui sont connectés avec une connexion VPN site à site sans aucune intervention manuelle. L’accès est transitif et les clients peuvent accéder aux ressources de tous les réseaux virtuels et sites (locaux) connectés.

![S2S à plusieurs réseaux virtuels et filiale](./media/vpn-gateway-about-point-to-site-routing/8.jpg "S2S à plusieurs réseaux virtuels et filiale")

### <a name="address-space"></a>Espace d’adressage

* VNet1 : 10.1.0.0/16

* VNet2 : 10.2.0.0/16

* VNet3 : 10.3.0.0/16

* Site1 : 10.101.0.0/16

### <a name="routes-added"></a>Routes ajoutées

* Routes ajoutées aux clients Windows : 10.1.0.0/16, 192.168.0.0/24

* Routes ajoutées aux clients non-Windows : 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accès

* Les clients Windows peuvent accéder à VNet1, VNet2, VNet3 et Site1, mais les routes vers VNet2, VNet3 et Site1 doivent être ajoutées manuellement dans le client.

* Les clients non-Windows peuvent accéder à VNet1, Vnet2, VNet3 et Site1.

## <a name="next-steps"></a>Étapes suivantes

Consultez [Créer un VPN P2S à l’aide du portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) pour commencer à créer votre VPN P2S.
