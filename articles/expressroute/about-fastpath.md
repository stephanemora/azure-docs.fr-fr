---
title: À propos d’Azure ExpressRoute FastPath
description: Découvrir Azure ExpressRoute FastPath pour envoyer le trafic en ignorant la passerelle réseau
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/10/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: b6483ef0e034f695da1e03475ca2a4b716773684
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532739"
---
# <a name="about-expressroute-fastpath"></a>À propos d’ExpressRoute FastPath

La passerelle de réseau virtuel ExpressRoute est conçue pour échanger des routages réseau et acheminer le trafic du réseau. FastPath est conçu pour améliorer les performances du chemin d’accès aux données entre votre réseau local et votre réseau virtuel. Lorsqu’il est activé, FastPath envoie le trafic réseau directement vers les machines virtuelles du réseau virtuel, en contournant la passerelle.

## <a name="requirements"></a>Configuration requise

### <a name="circuits"></a>Circuits

FastPath est disponible sur tous les circuits ExpressRoute.

### <a name="gateways"></a>Passerelles

FastPath nécessite toujours la création d’une passerelle réseau virtuelle pour échanger des routages entre le réseau virtuel et le réseau local. Pour plus d’informations sur les passerelles de réseau virtuel et ExpressRoute, y compris les informations de performances et les références SKU de passerelle, consultez [Passerelles de réseau virtuel ExpressRoute](expressroute-about-virtual-network-gateways.md).

Pour configurer FastPath, la passerelle de réseau virtuel doit être :

* Ultra-performance
* ErGw3AZ

> [!IMPORTANT]
> Si vous envisagez d’utiliser FastPath avec un Peering privé IPv6 sur ExpressRoute, veillez à sélectionner ErGw3AZ comme **référence (SKU)** . Notez que celle-ci n’est disponible que pour des circuits utilisant ExpressRoute Direct.
> 
>

## <a name="limitations"></a>Limites

Bien que FastPath prenne en charge la plupart des configurations, il ne prend pas en charge les fonctionnalités suivantes :

* Routage défini par l’utilisateur sur le sous-réseau de la passerelle : Ce routage défini par l’utilisateur (UDR) n’a aucun impact sur le trafic que FastPath envoie directement de votre réseau local aux machines virtuelles dans le réseau virtuel Azure. 

* Équilibreur de charge de base : si vous déployez un équilibreur de charge interne de base dans votre réseau virtuel ou si le service Azure PaaS que vous déployez dans votre réseau virtuel utilise un équilibreur de charge interne de base, le trafic réseau de votre réseau local vers les adresses IP virtuelles hébergées sur l’équilibreur de charge de base sera envoyé à la passerelle de réseau virtuel. La solution consiste à mettre à niveau l’équilibreur de charge de base vers un [équilibreur de charge standard](../load-balancer/load-balancer-overview.md).

* Private Link : si vous vous connectez à un [point de terminaison privé](../private-link/private-link-overview.md) dans votre réseau virtuel à partir de votre réseau local, la connexion passe par la passerelle de réseau virtuel.

## <a name="public-preview"></a>Préversion publique

Les fonctionnalités suivantes de FastPath sont disponibles en préversion publique :

**VNet peering** : FastPath envoie le trafic directement vers les machines virtuelles déployées dans un réseau virtuel homologué à celui connecté à ExpressRoute, en ignorant la passerelle de réseau virtuel ExpressRoute.

Consultez [Comment s’inscrire dans les fonctionnalités de ExpressRoute FastPath](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview).

Disponible dans toutes les régions.
 
## <a name="next-steps"></a>Étapes suivantes

Pour activer FastPath, consultez [Lier un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
