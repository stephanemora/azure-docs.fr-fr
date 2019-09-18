---
title: Configurer et valider des connexions de réseau virtuel ou de VPN
description: Guide pas à pas pour la configuration et la validation de différents déploiements de VPN et de réseau virtuel Azure
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901433"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>Configurer et valider des connexions de réseau virtuel ou de VPN

Cette procédure fournit des instructions pas à pas pour la configuration et la validation de différents déploiements de VPN et de réseau virtuel Azure dans des scénarios tels que l’itinéraire de transit, la connexion de réseau virtuel à réseau virtuel, la connexion multisite, la connexion point à site, etc.

Les passerelles VPN Azure permettent de disposer de presque n’importe quel type de topologie de réseau virtuel connecté (VNet) dans Azure : vous pouvez connecter des réseaux virtuels entre des régions, entre des types différents de réseaux virtuels (Azure Resource Manager et Classique), dans Azure ou avec un environnement hybride local, dans différents abonnements, etc. 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>Scénario 1 : Connexion VPN de réseau virtuel à réseau virtuel

La connexion entre deux réseaux virtuels est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité se servent d’une passerelle VPN pour fournir un tunnel sécurisé utilisant **Ipsec/IKE**. Les réseaux virtuels peuvent être situés dans des régions identiques ou différentes et appartenir à des abonnements identiques ou différents.

![IMAGE](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Figure 1 : de réseau virtuel à réseau virtuel avec IPsec

Si vos réseaux virtuels se trouvent dans la même région, vous pouvez envisager de les connecter à l’aide du service VNet Peering, qui n’utilise pas de passerelle VPN, augmente le débit et réduit la latence. Pour ce faire, sélectionnez **Configure and validate VNet Peering** (Configurer et valider VNET Peering) pour configurer une connexion d’appairage VNet.

Si vos réseaux virtuels ont tous deux été créés à l’aide du modèle de déploiement Azure Resource Manger, sélectionnez **Configure and validate a Resource Manager VNet to a Resource Manager VNet connection** (Configurer et valider une connexion de réseau virtuel Resource Manager à un réseau virtuel Resource Manager) pour configurer une connexion VPN.

Si l’un des réseaux virtuels a été créée à l’aide du modèle de déploiement Azure classique, et l’autre est créé par Resource Manager, sélectionnez**Configure and validate a classique VNet to a Resource Manager VNet connection** (Configurer et valider une connexion de réseau virtuel Classis à un réseau virtuel Resource Manager) pour configurer une connexion VPN.

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>Configuration 1 : Configurer un appairage pour deux réseaux virtuels situés dans la même région

Avant de commencer à appairer des réseaux virtuels Azure, assurez-vous de respecter les conditions préalables suivantes pour configurer VNET Peering :

* Les réseaux virtuels appairés doivent se trouver dans la même région Azure.
* Les réseaux virtuels appairés doivent avoir des espaces d’adressage IP qui ne se chevauchent pas.
* L’appairage se fait entre deux réseaux virtuels. Il n’y a aucune relation transitive dérivée entre les appairages. Par exemple, si VNetA est appairé avec VNetB et que VNetB est appairé avec VNetC, VNetA n’est *pas* appairé à VNetC.

Si ces exigences sont respectées, vous pouvez suivre le didacticiel [Créer un appairage de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) pour créer et configurer le service VNET Peering.

Pour vérifier la configuration du service VNET Peering, utilisez les méthodes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec un compte disposant des [rôles et autorisations](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions) nécessaires.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel pour lequel vous voulez créer un appairage.
4. Dans le volet qui s’affiche pour le réseau virtuel que vous avez sélectionné, dans la section **Paramètres**, cliquez sur **Appairages**.
5. Cliquez sur l’appairage dont vous souhaitez vérifier la configuration.

![IMAGE](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
À l’aide d’Azure PowerShell, exécutez la commande [AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) pour obtenir l’appairage de réseaux virtuels, par exemple :

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>Type de connexion 1 : Connectez un réseau virtuel Resource Manager à un autre réseau virtuel Resource Manager (connexion d’Azure Resource Manager à Azure Resource Manager)

Vous pouvez configurer une connexion d’un réseau virtuel Resource Manager à un autre réseau virtuel Resource Manager directement ou configurer la connexion avec IPsec.

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>Configuration 2 : Configurer une connexion VPN entre réseaux virtuels Resource Manager

Pour configurer une connexion entre réseaux virtuels Resource Manager sans IPSec, voir [Configurer une connexion de passerelle VPN de réseau virtuel à réseau virtuel à l’aide du portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Pour configurer une connexion avec IPSec entre deux réseaux virtuels Resource Manager, suivez les étapes 1 à 5 de la section [Créer une connexion site à site dans le portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) pour chaque réseau virtuel.

> [!Note]
> Ces étapes s’appliquent uniquement aux réseaux virtuels situés dans le même abonnement. Si vos réseaux virtuels figurent dans des abonnements différents, vous devrez utiliser PowerShell pour établir la connexion. Consultez l’article concernant [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>Valider une connexion VPN entre réseaux virtuels Resource Manager

![IMAGE](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Figure 4 – Connexion d’un réseau virtuel classique à un réseau virtuel Azure Resource Manager

Pour vérifier que votre connexion VPN est correctement configurée, suivez les instructions :

> [!Note]
> Les chiffres suivant les composants de réseau virtuel, tel que « Vnet 1 » dans les étapes ci-dessous correspondent aux chiffres présentés dans la figure 4.

1. Recherchez les espaces d’adressage qui se chevauchent dans le réseaux virtuels connecté.

   > [!Note]
   > Il ne peut pas y avoir d’espaces d’adressage se chevauchant dans les réseaux virtuels 1 et 6. 

2. Vérifiez que la plage d’adresses du réseau virtuel 1 d’Azure Resource Manager est définie correctement dans **Objet de connexion 4**.
3. Vérifiez que la plage d’adresses du réseau virtuel 6 d’Azure Resource Manager est définie correctement dans **Objet de connexion 3**.
4. Vérifiez que les clés prépartagées correspondent aux deux objets de connexion 3 et 4.
5. Vérifiez que l’adresse IP virtuelle de la passerelle de réseau virtuel 2 d’Azure Resource Manager est définie correctement dans **Objet de connexion 4**.
6. Vérifiez que l’adresse IP virtuelle de la passerelle de réseau virtuel 5 d’Azure Resource Manager est définie correctement dans **Objet de connexion 3**.

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>Type de connexion 2 : Connecter un réseau virtuel classique à un réseau virtuel Resource Manager

Vous pouvez créer une connexion entre des réseaux virtuels situés dans des abonnements différents et des régions différentes. Vous pouvez également connecter des réseaux virtuels déjà connectés à des réseaux locaux, pour autant que vous ayez configuré le type de passerelle en fonction du routage.

Pour plus d’informations sur la configuration d’une connexion entre un réseau virtuel classique et un réseau virtuel Resource Manager, voir [Connecter des réseaux virtuels de différents modèles de déploiement à l’aide du portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![IMAGE](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Figure 5 – Connexion d’un réseau virtuel classique à un réseau virtuel Azure Resource Manager

Pour vérifier la configuration de la connexion d’un réseau virtuel classique à un réseau virtuel Azure Resource Manager, suivez les instructions suivantes :

> [!Note]
> Les chiffres suivant les composants de réseau virtuel, tel que « Vnet 1 » dans les étapes ci-dessous correspondent aux chiffres présentés dans la figure 5.

1. Recherchez les espaces d’adressage qui se chevauchent dans le réseaux virtuels connecté.

   > [!Note]
   > Il ne peut pas y avoir d’espaces d’adressage se chevauchant dans les réseaux virtuels 1 et 6.

2. Vérifiez que la plage d’adresses du réseau virtuel 6 d’Azure Resource Manager est définie correctement dans la définition de réseau local classique 3.
3. Vérifiez que la plage d’adresses du réseau virtuel 1 classique est définie correctement dans l’**Objet de connexion 4** d’Azure Resource Manager.
4. Vérifiez que l’adresse IP virtuelle de la passerelle de réseau virtuel 2 classique est définie correctement dans l’**Objet de connexion 4** d’Azure Resource Manager.
5. Vérifiez que l’adresse IP virtuelle de la passerelle de réseau virtuel 5 d’Azure Resource Manager est définie correctement dans la **définition de réseau local** classique 3.
6. Vérifiez que les clés prépartagées correspondent aux deux réseaux virtuels connectés :
   1. Réseau virtuel classique – Définition de réseau local 3
   2. Réseau virtuel Azure Resource Manager – Objet de connexion 4

## <a name="scenario-2-point-to-site-vpn-connection"></a>Scénario 2 : Connexion VPN de point à site

Une configuration point à site (P2S) vous permet de connecter de manière sécurisée un ordinateur client individuel à un réseau virtuel. Les connexions point à site sont utiles lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel. La connexion VPN P2S est initiée à partir de l’ordinateur client à l’aide du client VPN Windows natif. Les clients qui se connectent utilisent des certificats pour l’authentification.

![IMAGE](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Figure 2 – Connexion point à site

Les connexions point à site ne nécessitent aucun appareil VPN. P2S crée les connexions VPN sur SSTP (Secure Socket Tunneling Protocol). Vous pouvez établir une connexion point à site à un réseau virtuel à l’aide d’autres outil et modèles de déploiement :

* [Configurer une connexion site à site vers un réseau virtuel à l’aide du portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Comment configurer une connexion point à site à un réseau virtuel à l’aide du portail Azure (classique)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Configurer une connexion site à site vers un réseau virtuel à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>Valider vos connexions point à site

L’article [Résolution des problèmes : problèmes de connexion point à site Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) vous guide dans la résolution des problèmes courants liés aux connexions point à site.

## <a name="scenario-3-multi-site-vpn-connection"></a>Scénario 3 : Connexion VPN multisite

Vous pouvez ajouter une connexion site à site (S2S) à un réseau virtuel qui possède déjà une connexion S2S, une connexion point à site ou une connexion de réseau virtuel à réseau virtuel. Dans ce cas, on parle souvent **configuration multisite**. 

![IMAGE](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Figure 3 – Connexion multisite

Actuellement, Azure utilise deux modèles de déploiement : Resource Manager et réseau classique. Les deux modèles ne sont pas entièrement compatibles entre eux. Pour savoir comment configurer une **connexion multisite** avec des modèles différents, voir les articles suivants :

* [Ajouter une connexion site à site à un réseau virtuel avec une connexion de passerelle VPN existante](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Ajouter une connexion site à site à un réseau virtuel avec une connexion de passerelle VPN existante (classique)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Ces étapes ne s’appliquent pas à des configurations de connexion ExpressRoute et site à site coexistantes. Pour en savoir plus sur les connexions coexistantes, voir [Connexions coexistantes ExpressRoute/S2S](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="scenario-4-configure-transit-routing"></a>Scénario 4 : Configurer un itinéraire de transit

L’itinéraire transitif est un scénario de routage spécifique dans lequel vous connectez plusieurs réseaux dans une topologie de connexion en chaîne. Ce routage permet à des ressources de réseaux virtuels à chaque extrémité d’une « chaîne » de communiquer entre elles par le biais de réseaux virtuels. Sans itinéraire transitif, des réseaux ou appareils appairés via un hub ne peuvent pas communiquer.

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>Configuration 1 : Configurer un itinéraire de transit dans une connexion point à site

Dans ce scénario, vous configurez une connexion VPN site à site entre les réseaux virtuels VNetA et VNetB, ainsi qu’un VPN point à site pour permettre au client de se connecter à la passerelle du réseau virtuel VNetA. Ensuite, vous pouvez activer un itinéraire de transit pour que les clients point à site puissent se connecter au réseau virtuel VNetB qui transite par le réseau virtuel VNetA. Ce scénario est pris en charge lorsque le protocole BGP est activé sur le VPN site à site entre les réseaux virtuels VNetA et VNetB. Pour plus d’informations, voir [À propos du routage VPN point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>Configuration 2 : Configurer un itinéraire de transit dans une connexion ExpressRoute

Microsoft Azure ExpressRoute vous permet d'étendre vos réseaux locaux au cloud de Microsoft via une connexion privée dédiée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud computing Microsoft, comme Microsoft Azure, Office 365 et Dynamics 365.  Pour plus d’informations, voir [Appairage ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![IMAGE](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

Figure 6 – Connexion d’« Appairage privé » ExpressRoute à des réseaux virtuels Azure

> [!Note]
> Si les réseaux virtuels VNetA et VNetB se trouvent dans la même région géopolitique, nous vous recommandons de [lier les deux réseaux virtuels au circuit ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) au lieu de configurer un itinéraire transitif. Si vos réseaux virtuels se trouvent dans des régions géopolitiques différentes, vous pouvez également les lier directement à votre circuit si vous disposez d’[ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Si vous avez ExpressRoute et une coexistence site à site, l’itinéraire de transit n’est pas pris en charge. Pour plus d’informations, voir [Configurer la coexistence de connexions ExpressRoute et site à site](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Si vous avez activé ExpressRoute pour connecter vos réseaux locaux à un réseau virtuel Azure, vous pouvez activer un appairage des réseaux virtuels pour lesquels vous souhaitez disposer d’un itinéraire transitif. Pour permettre à vos réseaux locaux de se connecter au réseau virtuel distant, vous devez configurer un [Appairage de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> L’appairage de réseaux virtuels n’est disponible que pour des réseaux virtuels situés dans la même région.

Pour vérifier si vous avez configuré l’itinéraire de transit pour l’appairage de réseaux virtuels, suivez les instructions :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec un compte disposant des [rôles et autorisations](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions) nécessaires.
2. [Créez un appairage entre les réseaux virtuels A et B](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) comme dans le diagramme ci-dessus (figure 6). 
3. Dans le volet qui s’affiche pour le réseau virtuel que vous avez sélectionné, dans la section **Paramètres**, cliquez sur **Appairages**.
4. Cliquez sur l’appairage que vous souhaitez afficher et sur **Configuration** pour confirmer que vous avez activé l’option **Autoriser le transit par passerelle** sur le réseau virtuel VNetA connecté au circuit ExpressRoute et l’option **Utiliser la passerelle distante** sur le réseau virtuel VNetB distant non connecté au circuit ExpressRoute.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>Configuration 3 : Configurer un itinéraire de transit dans une connexion d’appairage de réseaux virtuels

Lorsque des réseaux virtuels sont appairés, vous pouvez également configurer la passerelle du réseau virtuel appairé en tant que point de transit vers un réseau local. Pour configurer un itinéraire de transit dans un appairage de réseaux virtuels, vérifiez les [connexions de réseau virtuel à réseau virtuel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Le transit de la passerelle n’est pas pris en charge dans la relation d’appairage entre des réseaux virtuels créés via des modèles de déploiement différents. Les deux réseaux virtuels dans la relation d’appairage doivent avoir été créés via Resource Manager pour qu’un transit de la passerelle fonctionne.

Pour vérifier si vous avez configuré l’itinéraire de transit pour l’appairage de réseaux virtuels, suivez les instructions :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec un compte disposant des [rôles et autorisations](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions) nécessaires.
2. Dans la boîte de dialogue contenant le texte Rechercher des ressources en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel dont vous voulez vérifier le paramètre appairage.
4. Dans le volet qui s’affiche pour le réseau virtuel que vous avez sélectionné, dans la section **Paramètres**, cliquez sur **Appairages**.
5. Cliquez sur l’appairage que vous souhaitez afficher et vérifiez que vous avez activé les options **Autoriser le transit par passerelle** et **Utiliser la passerelle distante** sous **Configuration**.

![IMAGE](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>Configuration 4 : Configurer un itinéraire de transit dans une connexion de réseau virtuel à réseau virtuel

Pour configurer un itinéraire de transit entre réseaux virtuels, vous devez activer le protocole BGP sur toutes les connexions de réseau virtuel à réseau virtuel intermédiaires à l’aide du modèle de déploiement Resource Manager et de PowerShell. Pour obtenir des instructions, voir [Configurer BGP sur des passerelles VPN Azure à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Le routage du trafic via une passerelle VPN Azure est possible à l’aide du modèle de déploiement classique, mais il s’appuie sur des espaces d’adressage définis de manière statique dans le fichier de configuration réseau. Le protocole BGP n’est pas encore pris en charge avec les réseaux virtuels Azure et les passerelles VPN à l’aide du modèle de déploiement classique. Sans le protocole BGP, la définition manuelle d’espaces d’adressage de transit peut entraîner des erreurs et n’est pas recommandée.

> [!Note]
> Les connexions de réseau virtuel à réseau virtuel classiques sont configurées via le portail Azure (classique) ou à l’aide d’un fichier de configuration réseau dans le portail classique. Vous ne pouvez pas créer ou modifier un réseau virtuel classique via le modèle de déploiement Azure Resource Manager ou le portail Azure. Pour plus d’informations sur l’itinéraire de transit pour des réseaux virtuels classiques, voir [Hub&Spoke, Daisy-Chain and Full-Mesh VNET topologies in Azure ARM using VPN (V1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/) (Topologies de réseau virtuel Hub and Spoke, de connexion en chaîne et de maille pleine dans Azure ARM utilisant un VPN (V1)).

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>Configuration 5 : Configurer un itinéraire de transit dans une connexion site à site

Pour configurer l’itinéraire de transit entre votre réseau local et un réseau virtuel avec une connexion site à site, vous devez activer le protocole BGP sur toutes les connexions site à site intermédiaires à l’aide du modèle de déploiement Resource Manager et de PowerShell. Pour obtenir des instructions voir [Configurer BGP sur des passerelles VPN Azure à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Le routage du trafic via une passerelle VPN Azure est possible à l’aide du modèle de déploiement classique, mais il s’appuie sur des espaces d’adressage définis de manière statique dans le fichier de configuration réseau. Le protocole BGP n’est pas encore pris en charge avec les réseaux virtuels Azure et les passerelles VPN à l’aide du modèle de déploiement classique. Sans le protocole BGP, la définition manuelle d’espaces d’adressage de transit peut entraîner des erreurs et n’est pas recommandée.

> [!Note]
> Les connexions site à site classiques sont configurées via le portail Azure (classique) ou à l’aide d’un fichier de configuration réseau dans le portail classique. Vous ne pouvez pas créer ou modifier un réseau virtuel classique via le modèle de déploiement Azure Resource Manager ou le portail Azure. Pour plus d’informations sur l’itinéraire de transit pour des réseaux virtuels classiques, voir [Hub&Spoke, Daisy-Chain and Full-Mesh VNET topologies in Azure ARM using VPN (V1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/) (Topologies de réseau virtuel Hub and Spoke, de connexion en chaîne et de maille pleine dans Azure ARM utilisant un VPN (V1)).

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>Scénario 5 : Configurer BGP pour une passerelle VPN

BGP est le protocole de routage standard utilisé sur Internet pour échanger des informations de routage et d’accessibilité entre plusieurs réseaux. Lorsque le protocole BGP est utilisé dans le contexte de réseaux virtuels Azure, il active les passerelles VPN Azure et vos appareils VPN locaux, appelés pairs ou voisins BGP. Ceux-ci échangent des « itinéraires » informent les deux passerelles sur la disponibilité et l’accessibilité des préfixes pour le passage à travers les passerelles ou routeurs impliqués. Le protocole BGP assure également le routage de transit entre plusieurs réseaux en propageant les itinéraires qu’une passerelle BGP obtient d’un pair BGP à tous les autres pairs BGP. Pour plus d’informations, voir [Vue d’ensemble du protocole BGP avec les passerelles VPN Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configurer BGP pour une connexion VPN

Pour configurer une connexion VPN utilisant le protocole BGP, voir [Configurer BGP sur des passerelles VPN Azure à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

> [!Note]
> 1. Activez le protocole BGP sur la passerelle de réseau virtuel en créant un numéro AS pour celui-ci. Les passerelles de base ne prennent pas en charge le protocole BGP. Pour vérifier la référence (SKU) de la passerelle, accédez à la section Vue d’ensemble du panneau Passerelle VPN dans le portail Azure. Si votre référence (SKU) est **De base**, vous devez modifier la référence (SKU) (en [redimensionnant la passerelle](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) en **VpnGw1**. La vérification de la référence (SKU) peut entraîner un temps d’arrêt de 20 à 30 minutes. Dès que la passerelle dispose de la référence (SKU) correcte, vous pouvez ajouter le numéro AS via la cmdlet PowerShell [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0). Une fois le numéro AS configuré, une adresse IP de pair BGP pour la passerelle est fournie automatiquement.
> 2. La valeur LocalNetworkGateway doit être fournie **manuellement** avec un numéro As et une adresse de pair BGP. Vous pouvez définir les valeurs **ASN** et **-BgpPeeringAddress** à l’aide de la cmdlet PowerShell [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) ou [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0). Certains numéros As sont réservés à Azure et vous ne pouvez pas les utiliser de la manière décrite dans [À propos du protocole BGP avec une passerelle VPN Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq).
> 3. Enfin, le protocole BGP doit être activé sur l’objet de connexion. Vous pouvez définir la valeur **-EnableBGP** sur `$True` via la cmdlet [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) ou [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Valider la configuration BGP

Pour vérifier si le protocole BGP est correctement configuré, vous pouvez exécuter la cmdlet `get-AzureRmVirtualNetworkGateway` et `get-AzureRmLocalNetworkGateway`. Vous verrez la sortie liée au protocole BGP dans la partie BgpSettingsText. Par exemple :  BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>Scénario 6 : Connexion VPN active/active hautement disponible

Les principales différences entre les passerelles en modes actif/actif et actif/passif sont les suivantes :

* Vous devez créer deux configurations IP de passerelle avec deux adresses IP publiques
* Vous devez définir l’indicateur *EnableActiveActiveFeature*.
* La référence (SKU) de la passerelle doit être VpnGw1, VpnGw2, VpnGw3

Pour obtenir une haute disponibilité des connexions intersites et de réseau virtuel à réseau virtuel, vous devez déployer plusieurs passerelles VPN, et établir plusieurs connexions parallèles entre vos réseaux et Azure. Pour une vue d’ensemble des options de connectivité et de topologie, voir [Configuration haute disponibilité pour la connectivité entre réseaux locaux et entre réseaux virtuels](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Pour créer des connexions entre réseaux locaux et entre réseaux virtuels en mode actif/actif, suivez les instructions de la section [Configurer des connexions VPN S2S en mode actif/actif avec des passerelles VPN Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) pour configurer une passerelle VPN Azure en mode actif/actif.

> [!Note]  
> 1. Lorsque vous ajoutez des adresses à la passerelle de réseau local pour le protocole BGP activé, le mode actif/actif *ajoute uniquement les adresses/32 des pairs BGP*. Si vous ajoutez des autres adresses, celles-ci sont considérées comme des itinéraires statiques et ont priorité sur les itinéraires BGP.
> 2. Vous devez utiliser différents ASN BGP pour vos réseaux locaux se connectant à Azure. S’ils sont identiques, vous devez modifier l’ASN de votre réseau si votre appareil VPN local utilise déjà cet ASN pour s’appairer avec des voisins BGP.

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>Scénario 7 : Modifier un type de passerelle VPN Azure après le déploiement

Vous ne pouvez pas modifier une passerelle de réseau virtuelle Azure basée sur une stratégie en passerelle basée sur un itinéraire ou inversement. Vous devez supprimer la passerelle. Ensuite, l’adresse IP et la clé prépartagée (PSK) ne sont pas conservées. Vous pouvez alors créer une nouvelle passerelle du type souhaité. Pour supprimer et créer une passerelle, procédez comme suit :

1. Supprimez toutes les connexions associées à la passerelle d’origine.
2. Supprimez la passerelle à l’aide du portail Azure, de PowerShell ou de PowerShell classique : 
   * [Supprimer une passerelle de réseau virtuel à l’aide du portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Supprimer une passerelle de réseau virtuel à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Supprimer une passerelle de réseau virtuel à l’aide de PowerShell (classique)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Suivez les étapes de la section [Créer la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway) pour créer la nouvelle passerelle du type souhaité et achever l’installation du VPN.

> [!Note]
> Ce processus prend environ 60 minutes.

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes de connectivité entre machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

