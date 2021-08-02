---
title: Connecter un réseau virtuel à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez comment connecter un réseau virtuel à SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/1/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: e451f969c1518a920f7828d92fdfed65cd80f69c
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412382"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Connecter un réseau virtuel à de grandes instances HANA

Vous avez [créé un réseau virtuel Azure](hana-connect-azure-vm-large-instances.md). Vous pouvez maintenant connecter ce réseau à de grandes instances SAP HANA (autrement dit, à des instances BareMetal Infrastructure). Dans cet article, nous allons examiner les étapes que vous devez suivre.

## <a name="create-an-azure-expressroute-gateway-on-the-virtual-network"></a>Créer une passerelle Azure ExpressRoute sur le réseau virtuel

En premier lieu, créez une passerelle Azure ExpressRoute sur votre réseau virtuel. Cette passerelle vous permet de lier le réseau virtuel au circuit ExpressRoute qui se connecte à votre locataire sur le tampon de grande instance HANA.

> [!NOTE] 
> Cette étape peut prendre jusqu’à 30 minutes. Vous créez la nouvelle passerelle dans l’abonnement Azure désigné, puis la connectez au réseau virtuel Azure spécifié.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

- S’il existe déjà une passerelle, vérifiez s’il s’agit d’une passerelle ExpressRoute. Dans la négative, supprimez la passerelle et recréez-la en tant que passerelle ExpressRoute. Si une passerelle ExpressRoute est déjà établie, passez à la section suivante de cet article, [Lier des réseaux virtuels](#link-virtual-networks). 

- Utilisez soit le [portail Azure](https://portal.azure.com/), soit PowerShell pour créer une passerelle VPN ExpressRoute connectée à votre réseau virtuel.
    - Si vous utilisez le portail Azure, ajoutez une nouvelle **passerelle de réseau virtuel**, puis sélectionnez **ExpressRoute** comme type de passerelle.
    - Si vous utilisez PowerShell, commencez par télécharger et par utiliser le dernier [SDK Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
    Les commandes suivantes créent une passerelle ExpressRoute. Le texte précédé par un _$_ correspond à des variables définies par l’utilisateur qui doivent être mises à jour avec vos propres informations.

    ```powershell
    # These Values should already exist, update to match your environment
    $myAzureRegion = "eastus"
    $myGroupName = "SAP-East-Coast"
    $myVNetName = "VNet01"
    
    # These values are used to create the gateway, update for how you wish the GW components to be named
    $myGWName = "VNet01GW"
    $myGWConfig = "VNet01GWConfig"
    $myGWPIPName = "VNet01GWPIP"
    $myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance
    
    # These Commands create the Public IP and ExpressRoute Gateway
    $vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
    -Location $myAzureRegion -AllocationMethod Dynamic
    $gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
    $gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
    -PublicIpAddressId $gwpip.Id
    
    New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
    -IpConfigurations $gwipconfig -GatewayType ExpressRoute `
    -GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
    ```

La seule référence SKU de passerelle prise en charge pour SAP HANA sur Azure (grandes instances) est **UltraPerformance**.

## <a name="link-virtual-networks"></a>Lier des réseaux virtuels

Le réseau virtuel Azure a maintenant une passerelle ExpressRoute. Utilisez les informations d’autorisation fournies par Microsoft pour connecter la passerelle ExpressRoute au circuit de grandes instances ExpressRoute SAP HANA. Vous pouvez vous connecter à l’aide du portail Azure ou de PowerShell. Les instructions PowerShell sont les suivantes. 

Exécutez les commandes suivantes pour chaque passerelle ExpressRoute, en utilisant un AuthGUID différent pour chaque connexion. Les deux premières entrées dans le script suivant proviennent des informations fournies par Microsoft. En outre, AuthGUID est spécifique à chaque réseau virtuel et à sa passerelle. Si vous souhaitez ajouter un autre réseau virtuel Azure, vous devez obtenir un autre AuthID pour votre circuit ExpressRoute, qui connecte les grandes instances HANA à Azure de Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Le dernier paramètre de la commande New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** est un nouveau paramètre qui active ExpressRoute FastPath. Cette fonctionnalité a été ajoutée en mai 2019 pour réduire la latence réseau entre vos unités de grande instance HANA et les machines virtuelles Azure. Pour plus d’informations, consultez [Architecture réseau SAP HANA (grandes instances)](./hana-network-architecture.md). Avant d’exécuter les commandes, assurez-vous d’exécuter la dernière version des applets de commande PowerShell.

Vous devrez peut-être connecter la passerelle à plusieurs circuits ExpressRoute associés à votre abonnement. Dans ce cas, vous devrez exécuter plusieurs fois cette étape. Par exemple, vous allez probablement connecter la même passerelle de réseau virtuel au circuit ExpressRoute qui relie le réseau virtuel à votre réseau local.

## <a name="applying-expressroute-fastpath-to-existing-hana-large-instance-expressroute-circuits"></a>Application d’ExpressRoute FastPath à des circuits ExpressRoute de Grande instance HANA existants
Vous avez vu comment connecter un nouveau circuit ExpressRoute créé avec un déploiement de grande instance HANA à une passerelle Azure ExpressRoute dans l’un de vos réseaux virtuels Azure. Mais que faire si vos circuits ExpressRoute sont déjà configurés et que vos réseaux virtuels sont déjà connectés à de grandes instances HANA ? 

Le nouveau ExpressRoute FastPath réduit la latence du réseau. Nous vous recommandons d’appliquer cette modification pour tirer profit de cette latence réduite. Les commandes pour se connecter à un nouveau circuit ExpressRoute sont les mêmes que pour modifier un circuit ExpressRoute existant. Ainsi, vous devez exécuter cette séquence de commandes PowerShell pour modifier un circuit existant. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Il est important d’ajouter le dernier paramètre tel qu’il est affiché ci-dessus pour activer la fonctionnalité ExpressRoute FastPath.


## <a name="expressroute-global-reach"></a>Service Global Reach d’ExpressRoute

Activez Global Reach pour l’un des scénarios suivants :

 - Réplication de système HANA sans proxy ni pare-feu supplémentaire.
 - Copie des sauvegardes entre les unités de grande instance HANA dans deux régions différentes pour effectuer des copies du système ou pour les actualisations du système.

Pour activer Global Reach :

- Fournissez une plage d’espace d’adressage d’un espace d’adressage /29. Cette plage d’adresses ne doit chevaucher aucune autre plage d’espace d’adressage que vous avez utilisée jusqu’à présent pour connecter les grandes instances HANA à Azure. La plage d’adresses ne doit pas non plus chevaucher les plages d’adresses IP que vous avez utilisées ailleurs dans Azure ou localement.
- Il existe une limitation concernant les numéros de système autonome (ASN) utilisables pour annoncer vos routes locales vers les grandes instances HANA. Votre système local ne doit pas annoncer de routes avec des ASN privés dans la plage 65000 – 65020 ou 65515. 
- Lors de la connexion d’un accès direct local à de grandes instances HANA, vous devez calculer les frais liés au circuit qui vous connecte à Azure. Pour plus d’informations, consultez les tarifs liés au [module complémentaire Global Reach](https://azure.microsoft.com/pricing/details/expressroute/).

Pour appliquer l’un et/ou l’autre des scénarios à votre déploiement, adressez une demande de support à Azure en procédant de la manière décrite dans [Ouvrir une demande de support pour les Grandes instances HANA](./hana-li-portal.md#open-a-support-request-for-hana-large-instances).

Les données et les mots clés dont vous avez besoin pour que Microsoft route et exécute votre demande sont les suivants :

- Service : Grande instance SAP HANA
- Type de problème : Configuration et installation
- Sous-type de problème : Mon problème n’est pas listé ci-dessus.
- Objet « Modifier mon réseau – ajouter Global Reach »
- Détails : « Ajouter Global Reach à une grande instance HANA pour un locataire de grande instance HANA » ou « Ajouter Global Reach localement pour un locataire de grande instance HANA »
- Détails supplémentaires concernant le cas de la grande instance HANA pour un locataire de grande instance HANA : vous devez définir les **deux régions Azure** où se trouvent les deux locataires à connecter **ET** vous devez soumettre la **plage d’adresses IP /29**.
- Détails supplémentaires sur le cas de système local pour un locataire de grande instance HANA : 
    - Définissez la **région Azure** où est déployé le locataire de grande instance HANA auquel vous souhaitez vous connecter directement. 
    - Indiquez le **GUID d’authentification** et l’**ID de circuit pair** que vous avez reçus quand vous avez établi votre circuit ExpressRoute entre les sites locaux et Azure. 
    - Nommez votre **ASN**. 
    - Spécifiez une **plage d’adresses IP /29** pour ExpressRoute Global Reach.

> [!NOTE]
> Si vous souhaitez que les deux cas soient gérés, vous devez fournir deux plages d’adresses IP /29 différentes qui ne chevauchent aucune autre plage d’adresses IP utilisée à ce stade. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez d’autres exigences réseau possibles relatives au déploiement de grandes instances SAP HANA sur Azure.

> [!div class="nextstepaction"]
> [Exigences réseau supplémentaires pour les grandes instances](hana-additional-network-requirements.md)
