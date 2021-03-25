---
title: 'Configurer la coexistence de connexions à ExpressRoute et S2S VPN : Azure PowerShell'
description: Configurez une connexion ExpressRoute et une connexion VPN de site à site pouvant coexister pour le modèle Resource Manager à l’aide de PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/06/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 3b6ed39c11e3f90b986ef904ff3f8e9ff3158d0d
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574167"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Configurer ExpressRoute des connexions coexistantes de site à site en utilisant PowerShell
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - Classique](expressroute-howto-coexist-classic.md)
> 
> 

Cet article vous aide à configurer la coexistence de connexions ExpressRoute et VPN de site à site. La possibilité de configurer des connexions VPN de site à site et ExpressRoute présente plusieurs avantages. Vous pouvez configurer un VPN de site à site en tant que chemin de basculement sécurisé pour ExpressRoute, ou utiliser des VPN de site à site pour vous connecter à des sites qui ne sont pas connectés via ExpressRoute. Dans cet article, nous décrirons les étapes de configuration des deux scénarios. Cet article s'applique au modèle de déploiement Resource Manager.

La configuration de connexions VPN de site à site et ExpressRoute coexistantes possède plusieurs avantages :

* Vous pouvez configurer un réseau VPN de site à site comme un chemin d’accès de basculement sécurisé pour ExpressRoute. 
* Vous pouvez également utiliser des réseaux VPN de site à site pour vous connecter à des sites qui ne sont pas connectés via ExpressRoute. 

Les étapes de configuration de ces deux scénarios sont décrites dans cet article. Cet article concerne le modèle de déploiement Resource Manager et utilise PowerShell. Vous pouvez également configurer ces scénarios à l’aide du portail Azure, bien que la documentation ne soit pas encore disponible. Vous pouvez commencer par configurer chaque passerelle. En règle générale, vous ne subirez aucun temps d’arrêt lors de l’ajout d’une passerelle ou d’une connexion de passerelle.

>[!NOTE]
>Si vous souhaitez créer un VPN de site à site sur un circuit ExpressRoute, veuillez consulter [cet article](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Limites et limitations
* **Seule la passerelle VPN basée sur un itinéraire est prise en charge.** Vous devez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) basée sur un itinéraire. Vous pouvez également utiliser une passerelle VPN basée sur un itinéraire avec une connexion VPN configurée pour les « sélecteurs de trafic basés sur des stratégies », comme décrit dans [se connecter à plusieurs périphériques VPN basés sur des stratégies](../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).
* **L’ASN de la passerelle VPN Azure doit être défini sur 65515.** La passerelle VPN Azure prend en charge le protocole de routage BGP. Pour qu’ExpressRoute et Azure VPN fonctionnent ensemble, vous devez conserver la valeur par défaut (65515) du numéro ASN de votre passerelle Azure VPN. Si vous avez précédemment sélectionné un ASN autre que 65515 et que vous changez le paramètre en lui affectant la valeur 65515, vous devez réinitialiser la passerelle VPN pour que le paramètre prenne effet.
* **Le sous-réseau de la passerelle doit correspondre à /27 ou à un préfixe plus court**, (tel que /26,/25), à défaut de quoi un message d’erreur s’affiche lorsque vous ajoutez la passerelle de réseau virtuel ExpressRoute.
* **La coexistence dans un réseau virtuel à double pile n’est pas prise en charge.** Si vous utilisez la prise en charge IPv6 ExpressRoute et une passerelle ExpressRoute à double pile, la coexistence avec la passerelle VPN n’est pas possible.

## <a name="configuration-designs"></a>Modèles de configuration
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurer un réseau VPN de site à site comme un chemin d’accès de basculement pour ExpressRoute
Vous pouvez configurer une connexion VPN de site à site en tant que sauvegarde pour ExpressRoute. Cette connexion s’applique uniquement aux réseaux virtuels liés au chemin de peering privé Azure. Il n’existe aucune solution de basculement basée sur des réseaux VPN pour les services accessibles via le peering Azure Microsoft. Le circuit ExpressRoute est toujours le lien principal. Si le circuit ExpressRoute échoue, les données circulent via le chemin du réseau VPN de site à site uniquement. Pour éviter un routage asymétrique, la configuration de votre réseau local doit également privilégier le circuit ExpressRoute via la connexion VPN de site à site. Vous pouvez préférer le chemin d’accès ExpressRoute en définissant une préférence locale plus élevée pour les itinéraires recevant ExpressRoute. 

>[!NOTE]
> Si le peering Microsoft ExpressRoute est activé, vous pouvez recevoir l’adresse IP publique de votre passerelle Azure VPN sur la connexion ExpressRoute. Pour configurer votre connexion VPN site à site en tant que secours, vous devez configurer votre réseau local afin que la connexion VPN soit routée vers Internet.
>

> [!NOTE]
> Bien que le circuit ExpressRoute soit préférable au réseau VPN de site à site lorsque les deux itinéraires sont identiques, Azure utilise la correspondance de préfixe la plus longue pour choisir l’itinéraire vers la destination du paquet.
> 
> 

![Diagramme montrant une connexion VPN de site à site en tant que sauvegarde pour ExpressRoute.](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurer un réseau VPN de site à site pour se connecter à des sites non connectés via ExpressRoute
Vous pouvez configurer votre réseau là où certains sites se connectent directement à Azure via des réseaux VPN de site à site ou via ExpressRoute. 

![Coexister](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Vous ne pouvez pas configurer un réseau virtuel comme un routeur de transit.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Sélection des étapes à suivre
Vous avez le choix entre deux ensembles de procédures. La procédure de configuration que vous sélectionnez varie selon que vous disposez déjà d’un réseau virtuel auquel vous connecter ou que vous voulez créer un réseau virtuel.

* Je n’ai pas de réseau virtuel et dois en créer un
  
    Cette procédure vous guide dans la création d’un réseau virtuel si vous n’en possédez pas encore un. Elle vous demande d’utiliser le modèle de déploiement Resource Manager et de créer de nouvelles connexions ExpressRoute et VPN de site à site. Pour configurer un réseau virtuel, suivez les étapes décrites dans la section [Créer un réseau virtuel et des connexions qui coexistent](#new).
* J’ai déjà un réseau virtuel répondant au modèle de déploiement Resource Manager.
  
    Vous disposez peut-être déjà d’un réseau virtuel avec une connexion VPN de site à site existante ou une connexion ExpressRoute. Dans ce scénario, si le masque de sous-réseau de la passerelle est /28 ou inférieur (/28, /29, etc.), vous devez supprimer la passerelle existante. La section [Configurer des connexions qui coexistent pour un réseau virtuel existant](#add) vous guide tout au long des étapes de suppression de la passerelle puis de création de connexions ExpressRoute et VPN de site à site.
  
    Si vous supprimez et recréez votre passerelle, vous rencontrez des temps d’arrêt pour vos connexions intersites. Toutefois, vos machines virtuelles et services sont toujours en mesure de communiquer via l’équilibreur de charge lorsque vous configurez votre passerelle s’ils sont configurés pour le faire.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Créer un réseau virtuel et des connexions qui coexistent
Cette procédure vous guide dans la création d’un réseau virtuel et de connexions de site à site et ExpressRoute appelées à coexister. Les cmdlets que vous utilisez pour cette configuration peuvent être légèrement différentes de celles que vous connaissez. Utilisez les applets de commande spécifiées dans ces instructions.

1. Connectez-vous et sélectionnez votre abonnement.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Définissez les variables.

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Créez un réseau virtuel et un sous-réseau de passerelle. Pour en savoir plus sur la création d’un réseau virtuel, consultez [Créer un réseau virtuel](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Pour en savoir plus sur la création de sous-réseaux virtuels, consultez [Créer un sous-réseau virtuel](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
   
   > [!IMPORTANT]
   > Le sous-réseau de la passerelle doit être défini sur /27 ou un préfixe plus court (comme /26 ou /25).
   > 
   > 
   
    Créez un nouveau réseau virtuel.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Ajoutez des sous-réseaux.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Enregistrez la configuration du réseau virtuel.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Créez ensuite la passerelle VPN de site à site. Pour plus d’informations sur la configuration de la passerelle VPN, consultez la rubrique [Configuration d’un réseau virtuel avec une connexion de site à site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). La valeur GatewaySku est prise en charge uniquement pour les passerelles VPN *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* et *HighPerformance*. Les configurations de passerelle VPN et ExpressRoute coexistants ne sont pas prises en charge par la référence SKU De base. La valeur VpnType doit être *RouteBased*.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    La passerelle VPN Azure prend en charge le protocole de routage BGP. Vous pouvez spécifier le numéro ASN (numéro AS) pour ce réseau virtuel en ajoutant le commutateur -Asn dans la commande suivante. Si vous ne spécifiez pas ce paramètre, la valeur par défaut sera le numéro 65515.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    Vous pouvez trouver l’IP de peering BGP et le numéro AS qu’Azure utilise pour la passerelle VPN dans $azureVpn.BgpSettings.BgpPeeringAddress et $azureVpn.BgpSettings.Asn. Pour plus d’informations, consultez [Configurer BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) pour la passerelle VPN Azure.
5. Créez une entité de passerelle VPN de site local. Cette commande ne configure pas votre passerelle VPN locale. Elle vous permet d’indiquer les paramètres de la passerelle locale, par exemple l’adresse IP publique et l’espace d’adressage local afin que la passerelle VPN Azure puisse s’y connecter.
   
    Si votre périphérique VPN local prend uniquement en charge le routage statique, vous pouvez configurer des itinéraires statiques de la façon suivante :

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Si votre périphérique VPN local prend en charge le protocole BGP et que vous souhaitez activer le routage dynamique, vous devez connaître l’IP de peering BGP et le numéro AS utilisé par votre périphérique VPN local.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Configurez votre périphérique VPN local à connecter à la nouvelle passerelle VPN Azure. Pour plus d’informations sur la configuration du périphérique VPN, consultez la rubrique [Configuration de périphérique VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Liez la passerelle VPN de site à site dans Azure à la passerelle locale.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Si vous vous connectez à un circuit ExpressRoute existant, ignorez les étapes 8 et 9 et passez à l’étape 10. Configurez des circuits ExpressRoute. Pour plus d’informations sur la configuration du circuit ExpressRoute, consultez [Création d’un circuit ExpressRoute](expressroute-howto-circuit-arm.md).


9. Configurez un peering privé Azure via le circuit ExpressRoute. Pour plus d’informations sur la configuration de l’homologation privée Azure via le circuit ExpressRoute, consultez [configure peering](expressroute-howto-routing-arm.md) (Configurer l’homologation)

10. <a name="gw"></a>Créez une passerelle ExpressRoute. Pour plus d'informations sur la configuration de la passerelle ExpressRoute, consultez la rubrique [Configuration de la passerelle ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). La valeur de GatewaySKU doit être *Standard*, *HighPerformance*, ou *UltraPerformance*.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Liez la passerelle ExpressRoute au circuit ExpressRoute. Une fois cette étape terminée, la connexion entre votre réseau local et Azure est établie via ExpressRoute. Pour plus d'informations sur l'opération de liaison, voir l'article [Liaison de réseaux virtuels à ExpressRoute](expressroute-howto-linkvnet-arm.md).

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Pour configurer des connexions coexistantes pour un réseau virtuel existant
Si vous disposez d’un réseau virtuel qui n’a qu’une seule passerelle de réseau virtuel (disons une passerelle VPN de site à site) et que vous souhaitez ajouter une autre passerelle d’un autre type (par exemple, une passerelle ExpressRoute), vérifiez la taille de sous-réseau de passerelle. Si le sous-réseau de passerelle est /27 ou plus, vous pouvez ignorer les étapes ci-dessous et suivre les étapes décrites dans la section précédente pour ajouter une passerelle VPN de site à site ou une passerelle ExpressRoute. Si le sous-réseau de la passerelle est /28 ou /29, vous devez tout d’abord supprimer la passerelle de réseau virtuel et augmenter la taille du sous-réseau de la passerelle. Les étapes décrites dans cette section vous indiquent la marche à suivre.

Les cmdlets que vous utilisez pour cette configuration peuvent être légèrement différentes de celles que vous connaissez. Utilisez les applets de commande spécifiées dans ces instructions.

1. Supprimez la passerelle VPN ExpressRoute ou de site à site existante.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Supprimez le sous-réseau de la passerelle.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Ajoutez un sous-réseau de passerelle défini sur /27 ou plus.
   
   > [!NOTE]
   > S’il ne vous reste pas suffisamment d’adresses IP dans votre réseau virtuel pour augmenter la taille du sous-réseau de passerelle, vous devez augmenter l’espace d’adresses IP.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Enregistrez la configuration du réseau virtuel.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. À ce stade, vous disposez d’un réseau virtuel sans passerelle. Pour créer de nouvelles passerelles et établir les connexions, utilisez les exemples suivants :

   Définissez les variables.

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   Créez la passerelle.

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation> -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   Créez la connexion.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Pour ajouter une configuration point à site à la passerelle VPN

Vous pouvez suivre les étapes ci-dessous pour ajouter une configuration point à site à votre passerelle VPN dans une configuration de coexistence. Pour télécharger le certificat racine du VPN, vous devez installer PowerShell localement sur votre ordinateur ou utiliser le Portail Azure.

1. Ajoutez le pool d’adresses des clients VPN.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Téléchargez le [certificat racine](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#Certificates) VPN dans Azure pour votre passerelle VPN. Dans cet exemple, nous supposons que le certificat racine est stocké dans l'ordinateur local où sont exécutées les applets de commande PowerShell suivantes et que vous exécutez PowerShell localement. Vous pouvez également télécharger le certificat à l’aide du Portail Azure.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```
Pour plus d’informations sur le réseau VPN point à site, consultez la rubrique [Configuration d’une connexion point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="to-enable-transit-routing-between-expressroute-and-azure-vpn"></a>Pour activer le routage de transit entre ExpressRoute et Azure VPN
Si vous souhaitez activer la connectivité entre l’un de vos réseaux locaux connectés à ExpressRoute et un autre de vos réseaux locaux connecté à une connexion VPN site à site, vous devez configurer le [serveur de routage Azure](../route-server/expressroute-vpn-support.md).


## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
