---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/28/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3e4f5c07602d5bc1b7760793664415f092301c20
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444099"
---
### <a name="gwipnoconnection"></a> Pour modifier la passerelle de réseau local GatewayIpAddress - sans connexion de passerelle

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son adresse IP publique, vous devez modifier la passerelle de réseau local pour refléter cette modification. Utilisez l’exemple fourni pour modifier une passerelle de réseau local sans connexion de passerelle.

Lorsque vous modifiez cette valeur, vous pouvez également modifier les préfixes d’adresse en même temps. Veillez à utiliser le nom existant de votre passerelle de réseau local pour remplacer les paramètres actuels. Si vous utilisez un nom différent, vous créerez une nouvelle passerelle de réseau local au lieu de remplacer la passerelle existante.

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>Pour modifier la passerelle de réseau local GatewayIpAddress - avec une connexion de passerelle existante

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son adresse IP publique, vous devez modifier la passerelle de réseau local pour refléter cette modification. S’il existe déjà une connexion de passerelle, vous devez tout d’abord supprimer la connexion. Une fois la connexion supprimée, vous pouvez modifier l’adresse IP de la passerelle et recréer une connexion. Vous pouvez également modifier les préfixes d’adresse en même temps. Cela entraînera une interruption de votre connexion VPN. Lorsque vous modifiez l’adresse IP de la passerelle, vous n’avez pas besoin de supprimer la passerelle VPN. Vous devez uniquement supprimer la connexion.
 

1. Supprimez la connexion. Vous trouverez le nom de votre connexion en utilisant l’applet de commande « Get-AzureRmVirtualNetworkGatewayConnection ».

   ```azurepowershell-interactive
   Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Modifiez la valeur « GatewayIpAddress ». Vous pouvez également modifier les préfixes d’adresse en même temps. Veillez à utiliser le nom existant de votre passerelle de réseau local pour remplacer les paramètres actuels. Si vous ne le faites pas, vous créerez une nouvelle passerelle de réseau local au lieu de remplacer la passerelle existante.

   ```azurepowershell-interactive
   New-AzureRmLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Créez la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion spécifié pour votre configuration. Pour les autres types de connexion, consultez la page sur [les applets de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Pour obtenir le nom de VirtualNetworkGateway, vous pouvez exécuter l’applet de commande « Get-AzureRmVirtualNetworkGateway ».
   
    Définissez les variables.

   ```azurepowershell-interactive
   $local = Get-AzureRMLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
   $vnetgw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Créez la connexion.

   ```azurepowershell-interactive 
   New-AzureRmVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```