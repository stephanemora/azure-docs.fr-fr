---
title: 'Connexion à un réseau virtuel à l’aide de P2S VPN et plusieurs types d’authentification : portail'
titleSuffix: Azure VPN Gateway
description: Connectez-vous à un réseau virtuel via P2S à l’aide de plusieurs types d’authentification dans le Portail Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743183"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Configuration d’une connexion point à site à un réseau virtuel à l’aide de plusieurs types d’authentification : Portail Azure

Cet article vous permet de connecter de façon sécurisée des clients individuels qui exécutent Windows, Linux ou macOS à un réseau virtuel Azure. Les connexions VPN point à site sont utiles lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un emplacement distant, par exemple lorsque vous travaillez à distance depuis votre domicile ou en conférence. La connexion P2S est une solution alternative au VPN de site à site lorsque seul un nombre restreint de clients doivent se connecter à un réseau virtuel. Les connexions de point à site ne nécessitent pas de périphérique VPN ou d’adresse IP publique. La connexion P2S crée la connexion VPN via SSTP (Secure Socket Tunneling Protocol) ou IKEv2. Pour plus d’informations sur le VPN de point à site, consultez l’article [À propos du VPN de point à site](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Diagramme de connexion point à site d’un ordinateur à un réseau virtuel Azure":::

Pour plus d’informations sur le VPN point à site, consultez [À propos du VPN point à site](point-to-site-about.md). Pour créer cette configuration à l’aide d’Azure PowerShell, consultez [Configurer un VPN point à site à l’aide d’Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Prérequis

Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

Plusieurs types d’authentification sur la même passerelle VPN sont pris en charge uniquement avec le type de tunnel OpenVPN.

### <a name="example-values"></a><a name="example"></a>Exemples de valeurs

Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article :

* **Nom du réseau virtuel :** VNet1
* **Espace d’adressage :** 10.1.0.0/16<br>Pour cet exemple, nous n’utilisons qu’un seul espace d’adressage. Vous pouvez avoir plusieurs espaces d’adressage pour votre réseau virtuel.
* **Nom du sous-réseau** : FrontEnd
* **Plage d'adresses du sous-réseau :** 10.1.0.0/24
* **Abonnement :** vérifiez que vous utilisez l’abonnement approprié si vous en possédez plusieurs.
* **Groupe de ressources :** TestRG1
* **Emplacement :** USA Est
* **GatewaySubnet :** 10.1.255.0/27<br>
* **Nom de la passerelle de réseau virtuel :** VNet1GW
* **Type de passerelle :** VPN
* **Type de VPN :** basé sur la route
* **Nom de l'adresse IP publique :** VNet1GWpip
* **Type de connexion :** Point à site
* **Pool d'adresses des clients :** 172.16.201.0/24<br>Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool d’adresses des clients.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Créer un réseau virtuel

Avant de commencer, assurez-vous que vous disposez d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Passerelle de réseau virtuel

Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

>[!NOTE]
>La référence SKU de la passerelle De base ne prend pas en charge le type de tunnel OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Pool d’adresses des clients

Le pool d’adresses des clients est une plage d’adresses IP privées que vous spécifiez. Les clients qui se connectent via un réseau virtuel de point à site reçoivent de façon dynamique une adresse IP de cette plage. Utilisez une plage d’adresses IP privées qui ne chevauche ni l’emplacement local à partir duquel vous vous connectez ni le réseau virtuel auquel vous souhaitez vous connecter. Si vous configurez plusieurs protocoles et que SSTP est l’un d’entre eux, le pool d’adresses configuré est réparti de manière égale entre les protocoles configurés.

1. Une fois la passerelle de réseau virtuel créée, accédez à la section **Paramètres** de la page Passerelle de réseau virtuel. Dans **Paramètres**, sélectionnez **Configuration de point à site**. Sélectionnez **Configurer** pour ouvrir la page de configuration.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Capture d’écran de la page de configuration de point à site." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Sur la page **Configuration de point à site**, vous pouvez configurer divers paramètres. Dans la zone **Pool d’adresses**, ajoutez la plage d’adresses IP privées que vous souhaitez utiliser. Les clients VPN reçoivent dynamiquement une adresse IP à partir de la plage que vous spécifiez. Le masque minimal de sous-réseau est de 29 bits pour la configuration active/passive, et de 28 bits pour la configuration active/active.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Capture d’écran du pool d’adresses.":::

1. Passez à la section suivante pour configurer les différents types d’authentification et de tunnels.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Types d’authentification et de tunnels

Dans cette section, vous allez configurer le type d’authentification et le type de tunnel. Dans la page **Configuration point à site**, si vous ne voyez pas **Type de tunnel** ou **Type d’authentification**, cela signifie que votre passerelle utilise la référence SKU De base. La référence SKU de base ne prend pas en charge IKEv2 ou l’authentification RADIUS. Si vous souhaitez utiliser ces paramètres, vous devez supprimer et recréer la passerelle à l’aide d’une autre référence SKU de passerelle.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Capture d’écran du type d’authentification.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Type de tunnel

Dans la page **Configuration de point à site**, sélectionnez **OpenVPN (SSL)** comme type de tunnel.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Type d'authentification

Pour le **Type d’authentification**, sélectionnez les types souhaités. Les options sont les suivantes :

* Certificat Azure
* RADIUS
* Azure Active Directory

En fonction du ou des types d’authentification sélectionnés, vous verrez différents champs de paramètres de configuration qui devront être remplis. Renseignez les informations requises et sélectionnez **Enregistrer** en haut de la page pour enregistrer tous les paramètres de configuration.

Pour plus d'informations sur le type d’authentification, consultez :

* [Certificat Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Package de configuration du client VPN

Les clients VPN doivent être configurés avec les paramètres de configuration de client. Le package de configuration du client VPN contient des fichiers où se trouvent des paramètres permettant de configurer les clients VPN en vue de se connecter à un réseau virtuel via une connexion P2S.

Pour obtenir des instructions sur la génération et l’installation des fichiers de configuration du client VPN, utilisez l’article correspondant à votre configuration :

* [Créer et installer des fichiers de configuration du client VPN avec des configurations d’authentification par certificat de connexions P2S Azure natives](point-to-site-vpn-client-configuration-azure-cert.md).
* [Authentification Azure Active Directory : Configurer un client VPN pour les connexions P2S de protocole OpenVPN](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Forum Aux Questions sur les connexions point à site

Cette section contient des informations issues des questions fréquentes (FAQ) concernant les configurations point à site. Pour plus d’informations sur la passerelle VPN, vous pouvez également consulter les [Questions fréquentes (FAQ) sur la passerelle VPN](vpn-gateway-vpn-faq.md).

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](../index.yml). Pour plus d’informations sur la mise en réseau et les machines virtuelles, consultez [Vue d’ensemble du réseau de machines virtuelles Azure et Linux](../virtual-machines/network-overview.md).

Pour plus d’informations sur la résolution des problèmes liés aux connexions de point à site, consultez l’article [Résolution des problèmes de connexion de point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
