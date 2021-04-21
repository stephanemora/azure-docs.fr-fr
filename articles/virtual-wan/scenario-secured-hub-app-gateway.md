---
title: Sécuriser le trafic entre Application Gateway et des pools de back-ends
titleSuffix: Azure Virtual WAN
description: 'Scénarios de routage : Sécuriser le trafic transitant par une passerelle applicative déployée dans un réseau virtuel spoke connecté à un hub Virtual WAN sécurisé.'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315246"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Scénario : Sécuriser le trafic entre Application Gateway et des pools de back-ends

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans ce scénario, le trafic d’un utilisateur pénètre dans Azure par le biais d’une passerelle applicative déployée dans un réseau virtuel spoke connecté à un hub Virtual WAN sécurisé (hub Virtual WAN avec un Pare-feu Azure). L’objectif est d’utiliser le Pare-feu Azure dans le hub virtuel sécurisé pour inspecter le trafic entre la passerelle applicative et les pools de back-ends.

Il existe deux modèles de conception spécifiques dans ce scénario, selon que la passerelle applicative et les pools de back-ends se trouvent dans le même réseau virtuel ou dans différents réseaux virtuels.

* **Scénario 1 :** La passerelle applicative et les pools de back-ends se trouvent dans le même réseau virtuel appairé à un hub Virtual WAN (sous-réseaux distincts).
* **Scénario 2 :** La passerelle applicative et les pools de back-ends se trouvent dans des réseaux virtuels distincts appairés à un hub Virtual WAN.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Scénario 1 - Même VNet

Dans ce scénario, la passerelle applicative et les pools de back-ends se trouvent dans le même réseau virtuel appairé à un hub Virtual WAN (sous-réseaux distincts).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Diagramme du scénario 1." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Workflow

Actuellement, les routes publiées de la table de routage Virtual WAN vers les réseaux virtuels spoke sont appliqués à l’ensemble du réseau virtuel, et non aux sous-réseaux du VNet spoke. Par conséquent, des routes définies par l’utilisateur sont nécessaires pour ce scénario. Pour plus d’informations sur les routes définies par l’utilisateur, consultez [Routes personnalisées de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md#user-defined).


1. Dans Azure Firewall Manager, sur le réseau virtuel spoke contenant la passerelle applicative et les pools de back-ends, sélectionnez **Activer le trafic Internet sécurisé** et **Activer le trafic privé sécurisé**.
1. Configurer des routes définies par l’utilisateur sur le sous-réseau de passerelle applicative

   * Pour être certain que la passerelle applicative puisse envoyer le trafic directement à Internet, spécifiez les routes définies par l’utilisateur suivantes :

     * **Préfixe d’adresse** : 0.0.0.0.0/0
     * **Tronçon suivant** : Internet

   * Pour être certain que la passerelle applicative puisse envoyer le trafic au pool de back-ends par le biais du Pare-feu Azure dans le hub Virtual WAN, spécifiez les routes définies par l’utilisateur suivantes :

      * **Préfixe d’adresse** : sous-réseau du pool de back-ends (10.2.0.0/24)
      * **Tronçon suivant** : IP privée du Pare-feu Azure

1. Configurez une route définie par l’utilisateur sur le sous-réseau du pool de back-ends.

   * **Préfixe d’adresse** : sous-réseau Application Gateway
   * **Tronçon suivant** : IP privée du Pare-feu Azure

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Scénario 2 : VNets différents

Dans ce scénario, la passerelle applicative et les pools de back-ends se trouvent dans des réseaux virtuels distincts appairés à un hub Virtual WAN.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Diagramme du scénario 2." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Workflow

Actuellement, les routes publiées de la table de routage Virtual WAN vers les réseaux virtuels spoke sont appliqués à l’ensemble du réseau virtuel, et non aux sous-réseaux du VNet spoke. Par conséquent, des routes définies par l’utilisateur sont nécessaires pour ce scénario. Pour plus d’informations sur les routes définies par l’utilisateur, consultez [Routes personnalisées de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md#user-defined).

1. Dans **Azure Firewall Manager**, sélectionnez **Activer le trafic Internet sécurisé** et **Activer le trafic privé sécurisé** sur les deux réseaux virtuels spoke.

1. Configurer des routes définies par l’utilisateur sur le sous-réseau de passerelle applicative Pour être certain que la passerelle applicative puisse envoyer le trafic directement à Internet, spécifiez les routes définies par l’utilisateur suivantes :

   * **Préfixe d’adresse** : 0.0.0.0.0/0
   * **Tronçon suivant** : Internet

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
* Pour plus d’informations sur les routes définies par l’utilisateur, consultez [Routes personnalisées de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md#user-defined).
* Pour plus d’informations sur les hubs virtuels sécurisés Virtual WAN, consultez [Hubs virtuels sécurisés](../firewall-manager/secured-virtual-hub.md).
