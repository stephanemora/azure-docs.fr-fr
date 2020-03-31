---
title: Héberger plusieurs sites à l’aide de PowerShell
titleSuffix: Azure Application Gateway
description: Découvrez comment créer une passerelle d’application qui héberge plusieurs sites à l’aide d’Azure Powershell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 42efec9f6c680572350005ac8152dcc31509c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295562"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-azure-powershell"></a>Créer une passerelle d’application hébergeant plusieurs sites à l’aide d’Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour configurer [l’hébergement de plusieurs sites web](application-gateway-multi-site-overview.md) quand vous créez une [passerelle d’application](application-gateway-introduction.md). Dans ce didacticiel, vous créez des pools backend à l’aide de groupes de machines virtuelles identiques. Vous configurez ensuite des écouteurs et des règles en fonction des domaines qui vous appartiennent pour vérifier que le trafic web arrive sur les serveurs appropriés dans les pools. Ce didacticiel, qui part du principe que vous avez plusieurs domaines, utilise *www.contoso.com* et *www.fabrikam.com* en guise d’exemples.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle Application Gateway
> * Créer des écouteurs et des règles de routage
> * Créer des groupes de machines virtuelles identiques avec les pools principaux
> * Créer un enregistrement CNAME dans votre domaine

![Exemple de routage multisite](./media/application-gateway-create-multisite-azureresourcemanager-powershell/scenario.png)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 1.0.0 ou version ultérieure pour les besoins de ce tutoriel. Pour trouver la version, exécutez `Get-Module -ListAvailable Az`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources Azure à l’aide de [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Configurez les sous-réseaux nommés *myBackendSubnet* et *myAGSubnet* à l’aide de [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Créez le réseau virtuel nommé *myVNet* à l’aide de [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) avec les configurations de sous-réseau. Enfin, créez l’adresse IP publique nommée *myAGPublicIPAddress* à l’aide de [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Ces ressources sont utilisées pour fournir la connectivité réseau à la passerelle d’application et à ses ressources associées.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Créer les configurations IP et le port frontal

Associez *myAGSubnet* créé précédemment à la passerelle d’application à l’aide de [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Affectez *myAGPublicIPAddress* à la passerelle d’application à l’aide de [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pools-and-settings"></a>Créer les pools backend et les paramètres

Créez les pools principaux nommés *contosoPool* et *fabrikamPool* pour la passerelle d’application à l’aide de [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configurez les paramètres pour le pool à l’aide de [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$fabrikamPool = New-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>Créer les écouteurs et les règles

Un écouteur est exigé pour permettre à la passerelle d’application d’acheminer le trafic de manière appropriée vers les pools backend. Ce didacticiel vous montre comment créer des écouteurs pour chacun de vos deux domaines. Dans cet exemple, des écouteurs sont créés pour les domaines *www.contoso.com* et *www.fabrikam.com*.

Créez les écouteurs nommés *contosoListener* et *fabrikamListener* à l’aide de [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) avec la configuration du frontend et le port frontal créés précédemment. Des règles sont exigées pour que les écouteurs sachent quel pool backend utiliser pour le trafic entrant. Créez des règles de base nommées *contosoRule* et *fabrikamRule* à l’aide de [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$contosolistener = New-AzApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"
$fabrikamlistener = New-AzApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"
$contosoRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
$fabrikamRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Maintenant que vous avez créé les ressources nécessaires pour la prise en charge, spécifiez des paramètres de la passerelle d’application nommée *myAppGateway* à l’aide de [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), puis créez-la à l’aide de [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>Créer des groupes de machines virtuelles identiques

Dans cet exemple, vous créez deux groupes de machines virtuelles identiques prenant en charge les deux pools backend créés. Ces groupes identiques sont nommés *myvmss1* et *myvmss2*. Chacun contient deux instances de machines virtuelles sur lesquelles IIS sera installé. Vous assignez le groupe identique au pool principal lorsque vous configurez les paramètres IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw
$fabrikamPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
  }
  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId
  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic
  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i
  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig
  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Installer IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="create-cname-record-in-your-domain"></a>Créer un enregistrement CNAME dans votre domaine

Une fois la passerelle d’application créée avec son adresse IP publique, vous pouvez obtenir l’adresse DNS et l’utiliser pour créer un enregistrement CNAME dans votre domaine. Vous pouvez utiliser [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) pour obtenir l’adresse DNS de la passerelle d’application. Copiez la valeur *fqdn* de DNSSettings et utilisez-la comme valeur de l’enregistrement CNAME que vous créez. L’utilisation d’enregistrements A n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer au moment du redémarrage de la passerelle d’application.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Entrez votre nom de domaine dans la barre d’adresse de votre navigateur. Par exemple, `https://www.contoso.com`.

![Tester le site contoso dans la passerelle d’application](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest.png)

Remplacez l’adresse par celle de votre autre domaine. Voici ce qui doit apparaître :

![Tester le site fabrikam dans la passerelle d’application](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest2.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle Application Gateway
> * Créer des écouteurs et des règles de routage
> * Créer des groupes de machines virtuelles identiques avec les pools principaux
> * Créer un enregistrement CNAME dans votre domaine

> [!div class="nextstepaction"]
> [En savoir plus sur ce que la passerelle d’application vous permet de faire](application-gateway-introduction.md)
