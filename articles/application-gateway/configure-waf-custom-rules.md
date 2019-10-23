---
title: Configurer des règles personnalisées de pare-feu d’applications web v2 à l’aide d’Azure PowerShell
description: Découvrez comment configurer des règles personnalisées de pare-feu d’applications web v2 à l’aide d’Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263747"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Configurer des règles personnalisées de pare-feu d’applications web v2 à l’aide d’Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Avec les règles personnalisées, vous pouvez créer vos propres règles, qui sont évaluées pour chaque requête qui passe par le pare-feu d’applications web (WAF). Ces règles ont une priorité plus élevée que les autres règles des ensembles de règles gérés. Les règles personnalisées ont une action (autoriser ou bloquer), une condition de correspondance, et un opérateur pour permettre une personnalisation complète.

Cet article crée un pare-feu d’applications web (WAF) v2 Azure Application Gateway qui utilise une règle personnalisée. Les règles personnalisées bloquent le trafic si l’en-tête de la requête contient User-Agent *evilbot*.

Pour voir d’autres exemples de règle personnalisée, consultez [Créer et utiliser des règles de pare-feu d’applications web personnalisées](create-custom-waf-rules.md).

Pour exécuter le code Azure PowerShell de cet article dans un script continu que vous pouvez copier, coller et exécuter, consultez [Exemples PowerShell Azure Application Gateway](powershell-samples.md).

## <a name="prerequisites"></a>Prérequis
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Vous avez besoin d’un module Azure PowerShell. Si vous choisissez d’installer et d’utiliser Azure PowerShell en local, ce script exige le module Azure PowerShell version 2.1.0 ou ultérieure. Effectuez les actions suivantes :

  1. Pour connaître la version de l’interface, exécutez `Get-Module -ListAvailable Az`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).
  2. Pour créer une connexion avec Azure, exécutez `Connect-AzAccount`.

## <a name="example-script"></a>Exemple de script

### <a name="set-up-variables"></a>Configurer des variables

Exécutez le code suivant :

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Exécutez le code suivant :

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Exécutez le code suivant :

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Créer une adresse IP virtuelle publique statique

Exécutez le code suivant :

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Créer un pool et un port frontal

Exécutez le code suivant :

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Créer un écouteur, un paramètre HTTP, une règle et une mise à l’échelle automatique

Exécutez le code suivant :

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Créer la règle personnalisée et l’appliquer à la stratégie de pare-feu d’applications Web

Exécutez le code suivant :

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Exécutez le code suivant :

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le pare-feu d’application Web](waf-overview.md)
