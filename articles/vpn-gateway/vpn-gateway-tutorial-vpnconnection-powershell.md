---
title: Créer et gérer des connexions VPN S2S Azure à l’aide de PowerShell | Microsoft Docs
description: 'Tutoriel : Créer et gérer des connexions VPN S2S avec le module Azure PowerShell'
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012935"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Créer et gérer des connexions VPN S2S avec le module Azure PowerShell

Les connexions VPN S2S Azure fournissent une connectivité sécurisée entre le réseau local du client et Azure. Ce tutoriel vous guide tout au long du cycle de vie d’une connexion VPN S2S IPsec. Il couvre notamment la création et la gestion d’une connexion VPN S2S. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une connexion VPN S2S
> * Mettre à jour la propriété de connexion : clé prépartagée, BGP, stratégie IPsec/IKE
> * Ajouter d’autres connexions VPN
> * Supprimer une connexion VPN

Le diagramme suivant montre la topologie de ce tutoriel :

![Diagramme d’une connexion VPN de site à site](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.3 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="requirements"></a>Configuration requise

Suivez le premier tutoriel, intitulé « [Créer une passerelle VPN avec Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md) », pour créer les ressources suivantes :

1. Groupe de ressources (TestRG1), réseau virtuel (VNet1) et GatewaySubnet
2. Passerelle VPN (VNet1GW)

Les valeurs des paramètres du réseau virtuel sont répertoriées ci-dessous. Notez la présence de valeurs supplémentaires pour la passerelle de réseau local afin de représenter votre réseau local. Changez les valeurs en fonction de l’environnement et de la configuration réseau.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

La procédure de création d’un réseau VPN S2S est simple :

1. Créez une passerelle de réseau local pour représenter votre réseau local.
2. Créez une connexion entre votre passerelle VPN Azure et la passerelle de réseau local.

## <a name="create-a-local-network-gateway"></a>Créer une passerelle de réseau local

Une passerelle de réseau local représente votre réseau local. Vous pouvez spécifier les propriétés de votre réseau local dans la passerelle de réseau local, notamment :

* Adresse IP publique de votre périphérique VPN
* Espace d’adressage local
* (Facultatif) Attributs BGP (adresse IP et numéro AS de l’homologue BGP)

Créez une passerelle de réseau local avec la commande [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Créer une connexion VPN S2S

Créez ensuite une connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN avec la commande [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection). Notez que la valeur de « -ConnectionType » pour le VPN de site à site est *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Ajoutez la propriété facultative « **-EnableBGP $True** » pour activer BGP pour la connexion si vous utilisez BGP. Il est désactivé par défaut.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Mettre à jour la clé prépartagée, BGP et la stratégie IPsec/IKE de la connexion VPN

### <a name="view-and-update-your-pre-shared-key"></a>Afficher et mettre à jour la clé prépartagée

Une connexion VPN S2S Azure utilise une clé prépartagée (secret) pour l’authentification entre votre périphérique VPN local et la passerelle VPN Azure. Vous pouvez afficher et mettre à jour la clé prépartagée pour une connexion avec [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) et [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> La clé prépartagée est une chaîne qui ne dépasse pas 128 **caractères ASCII imprimables**.

Cette commande montre la clé prépartagée de la connexion :

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

Étant donné l’exemple ci-dessus, le résultat est « **Azure@!b2C3** ». Utilisez la commande ci-dessous pour remplacer la valeur de la clé prépartagée par « **Azure@!_b2=C3** » :

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Activer BGP sur une connexion VPN

Une passerelle VPN Azure prend en charge le protocole de routage dynamique BGP. Vous pouvez activer BGP sur chaque connexion, selon que vous utilisez BGP dans vos périphériques et réseaux locaux. Spécifiez les propriétés BGP suivantes avant d’activer BGP sur la connexion :

* ASN (numéro de système autonome) du VPN Azure
* ASN de la passerelle de réseau local
* Adresse IP de l’homologue BGP de la passerelle de réseau local

Si vous n’avez pas configuré les propriétés BGP, utilisez les commandes suivantes pour ajouter ces propriétés à la passerelle VPN et à la passerelle de réseau local : [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) et [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Activez BGP avec [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Vous pouvez désactiver BGP en affectant à la propriété « -EnableBGP » la valeur **$False**. Pour obtenir des explications plus détaillées sur l’utilisation de BGP avec les passerelles VPN Azure, consultez [BGP sur les passerelles VPN Azure](vpn-gateway-bgp-resource-manager-ps.md).

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Appliquer une stratégie IPsec/IKE personnalisée sur la connexion

Au lieu d’utiliser les [propositions par défaut](vpn-gateway-about-vpn-devices.md#ipsec), vous pouvez appliquer une stratégie IPsec/IKE facultative pour spécifier la combinaison exacte d’algorithmes de chiffrement IPsec/IKE et de puissances de clé sur la connexion. L’exemple de script ci-dessous crée une stratégie IPsec/IKE différente avec les algorithmes et paramètres suivants :

* IKEv2 : AES256, SHA256, DHGroup14
* IPsec : AES128, SHA1, PFS14, 14 400 s (durée de vie de l’AS) et 102 400 000 Ko

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Pour obtenir la liste complète des algorithmes et des instructions, consultez [Stratégie IPsec/IKE pour les connexions S2S ou de réseau virtuel à réseau virtuel](vpn-gateway-ipsecikepolicy-rm-powershell.md).

## <a name="add-another-s2s-vpn-connection"></a>Ajouter une autre connexion VPN S2S

Pour ajouter une connexion VPN S2S à la même passerelle VPN, créez une autre passerelle de réseau local, puis créez une connexion entre cette passerelle et la passerelle VPN. Continuons de suivre l’exemple de cet article.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Vous avez désormais deux connexions VPN S2S à votre passerelle VPN Azure.

![Connexions VPN multisites](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Supprimer une connexion VPN S2S

Pour supprimer une connexion VPN S2S, utilisez [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Si vous n’avez plus besoin de la passerelle de réseau local, supprimez-la. Vous ne pouvez pas supprimer une passerelle de réseau local si elle est associée à d’autres connexions.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer et à gérer des connexions VPN S2S. Vous pouvez notamment :

> [!div class="checklist"]
> * Créer une connexion VPN S2S
> * Mettre à jour la propriété de connexion : clé prépartagée, BGP, stratégie IPsec/IKE
> * Ajouter d’autres connexions VPN
> * Supprimer une connexion VPN

Pour en savoir plus sur les connexions S2S, P2S et de réseau virtuel à réseau virtuel, accédez aux tutoriels suivants.

> [!div class="nextstepaction"]
> * [Créer des connexions de réseau virtuel à réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Créer des connexions P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
