---
title: Créer une passerelle d’application avec un arrêt SSL - Azure PowerShell | Microsoft Docs
description: Découvrez comment créer une passerelle d’application et ajouter un certificat pour un arrêt SSL à l’aide d’Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: e49706c529ca7d2c99f2c50f8de27063dbcd4da9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65202899"
---
# <a name="create-an-application-gateway-with-ssl-termination-using-azure-powershell"></a>Créer une passerelle d’application avec un arrêt SSL à l’aide d’Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour créer une [passerelle d’application](application-gateway-introduction.md) avec un certificat pour un [arrêt SSL](application-gateway-backend-ssl.md) qui utilise un [groupe de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pour des serveurs principaux. Dans cet exemple, le groupe identique contient deux instances de machine virtuelle qui sont ajoutées au pool backend par défaut de la passerelle d’application. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un certificat auto-signé
> * Configurer un réseau
> * Créer une passerelle d’application avec le certificat
> * Créer un groupe de machines virtuelles identiques avec le pool backend par défaut

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ce didacticiel requiert le module Azure PowerShell version 1.0.0 ou version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Dans un environnement de production, vous devez importer un certificat valide signé par un fournisseur approuvé. Pour ce didacticiel, vous créez un certificat auto-signé à l’aide de [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Vous pouvez utiliser [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) avec l’empreinte numérique qui a été retournée pour exporter un fichier pfx du certificat.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Un résultat comme l’exemple suivant devrait s’afficher :

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Utilisez l’empreinte numérique pour créer le fichier pfx :

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources Azure nommé *myResourceGroupAG* avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Configurez les sous-réseaux nommés *myBackendSubnet* et *myAGSubnet* à l’aide de [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Créez le réseau virtuel nommé *myVNet* à l’aide de [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) avec les configurations de sous-réseau. Enfin, créez l’adresse IP publique nommée *myAGPublicIPAddress* à l’aide de [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Ces ressources sont utilisées pour fournir la connectivité réseau à la passerelle d’application et à ses ressources associées.

```powershell
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

```powershell
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
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Créer le pool principal et les paramètres

Créez le pool back-end nommé *appGatewayBackendPool* pour la passerelle d’application à l’aide de [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configurez les paramètres pour le pool back-end à l’aide de [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```powershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Créer l’écouteur et la règle par défaut

Un écouteur est requis pour permettre à la passerelle d’application d’acheminer le trafic de manière appropriée vers le pool principal. Dans cet exemple, vous créez un écouteur de base qui écoute le trafic vers l’URL racine. 

Créer un objet de certificat à l’aide de [New-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate), puis créez un écouteur nommé *mydefaultListener* à l’aide de [ New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) avec la configuration de serveur frontal, le port frontal et le certificat que vous avez créés précédemment. Une règle est requise pour que l’écouteur sache quel pool principal utiliser pour le trafic entrant. Créez une règle de base nommée *rule1* à l’aide de [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-certificate"></a>Créer la passerelle d’application avec le certificat

Maintenant que vous avez créé les ressources nécessaires pour la prise en charge, spécifiez des paramètres de la passerelle d’application nommée *myAppGateway* à l’aide de [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), puis créez-la à l’aide de [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) avec le certificat.

### <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="create-a-virtual-machine-scale-set"></a>Créer un groupe de machines virtuelles identiques

Dans cet exemple, vous créez un groupe de machines virtuelles identiques pour fournir des serveurs pour le pool principal dans la passerelle d’application. Vous assignez le groupe identique au pool principal lorsque vous configurez les paramètres IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
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
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Installer IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Vous pouvez utiliser [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) pour obtenir l’adresse IP publique de la passerelle d’application. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Avertissement de sécurité](./media/application-gateway-ssl-arm/application-gateway-secure.png)

Pour accepter l’avertissement de sécurité si vous avez utilisé un certificat auto-signé, sélectionnez **Détails**, puis **Atteindre la page web**. Votre site IIS sécurisé apparaît maintenant comme dans l’exemple suivant :

![Tester l’URL de base dans la passerelle d’application](./media/application-gateway-ssl-arm/application-gateway-iistest.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un certificat auto-signé
> * Configurer un réseau
> * Créer une passerelle d’application avec le certificat
> * Créer un groupe de machines virtuelles identiques avec le pool principal par défaut

Pour plus d’informations sur les passerelles d’application et leurs ressources associées, consultez les articles de guide pratique.
