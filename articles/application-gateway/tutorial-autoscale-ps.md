---
title: Créer une passerelle d’application redondante dans une zone, avec une mise à l’échelle automatique et avec une adresse IP réservée - Azure PowerShell
description: Apprenez à créer une passerelle d’application redondante dans une zone, avec une mise à l’échelle automatique et avec une adresse IP réservée à l’aide d’Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 9/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6c54706f45653c43e6b41d0adb3132583079e6b6
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167524"
---
# <a name="tutorial-create-an-autoscaling-zone-redundant-application-gateway-with-a-reserved-virtual-ip-address-using-azure-powershell"></a>Didacticiel : Créer une passerelle d’application redondante dans une zone, avec une mise à l’échelle automatique et avec une adresse IP virtuelle réservée à l’aide d’Azure PowerShell

Ce didacticiel explique comment créer une passerelle Azure Application Gateway à l’aide des cmdlets Azure PowerShell et du modèle de déploiement Azure Resource Manager. Ce didacticiel se concentre sur les différences entre la nouvelle référence SKU de mise à l’échelle automatique et la référence SKU Standard existante. Plus précisément, les fonctionnalités de prise en charge de la mise à l’échelle automatique, de redondance de zone et d’adresses IP virtuelles réservées (adresses IP statiques).

Pour plus d’informations sur la mise à l’échelle de la passerelle d’application et la redondance de zone, consultez [Passerelle d’application redondante dans une zone et avec mise à l’échelle automatique (préversion publique)](application-gateway-autoscaling-zone-redundant.md).

> [!IMPORTANT]
> La référence SKU de la passerelle d’application redondante dans une zone et avec mise à l’échelle automatique est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le paramètre de configuration de mise à l’échelle automatique
> * Utiliser le paramètre de zone
> * Utiliser une adresse IP virtuelle statique
> * Créer la passerelle Application Gateway


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour ce didacticiel, vous devez exécuter Azure PowerShell localement. Vous devez disposer du module Azure PowerShell version 6.9.0 ou d’une version ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Après avoir vérifié la version PowerShell, exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créer un groupe de ressources dans l’un des emplacements disponibles.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-vnet"></a>Créer un réseau virtuel
Créez un réseau virtuel avec un sous-réseau dédié pour une passerelle d’application de mise à l’échelle automatique. Actuellement, une seule passerelle d’application de mise à l’échelle automatique peut être déployée dans chaque sous-réseau dédié.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Créer une adresse IP publique réservée

Spécification de la méthode d’allocation PublicIPAddress **Statique**. Une adresse IP virtuelle de passerelle d’application de mise à l’échelle automatique peut être uniquement statique. Les adresses IP dynamiques ne sont pas prises en charge. Seule la référence SKU PublicIpAddress standard est prise en charge.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Récupérer les détails

Récupérez les détails du groupe de ressources, du sous-réseau et de l’adresse IP dans un objet local pour créer des informations de configuration d’adresse IP de passerelle d’application.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```
## <a name="configure-application-gateway-infrastructure"></a>Configurer une infrastructure de passerelle d’application
Configurez l’adresse IP, l’adresse IP frontend, le pool principal, les paramètres HTTP, le certificat, le port, l’écouteur et la règle dans un format identique à celui de la passerelle Application Gateway Standard existante. La nouvelle référence SKU suit le même modèle d’objet que la référence SKU Standard.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Spécifier la mise à l’échelle automatique

Vous pouvez maintenant spécifier la configuration de mise à l’échelle automatique pour la passerelle d’application. Deux types de configuration de mise à l’échelle automatique sont pris en charge :

- **Mode de capacité fixe**. Dans ce mode, la passerelle d’application n’effectue pas de mise à l’échelle automatique et fonctionne à une capacité d’unité d’échelle fixe.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
- **Mode de mise à l’échelle automatique**. Dans ce mode, la passerelle d’application se met automatiquement à l’échelle en fonction du modèle de trafic d’application.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Créez une passerelle Application Gateway et incluez des zones de redondance. 

La configuration de zone est prise en charge uniquement dans les régions où les zones Azure sont disponibles. Dans les régions où les zones Azure ne sont pas disponibles, le paramètre de zone ne doit pas être utilisé. Une passerelle d’application peut également être déployée dans une zone unique, deux zones ou les trois zones. PublicIPAddress pour une passerelle d’application de zone unique doit être lié à la même zone. Pour une passerelle d’application redondante sur deux ou trois zones, PublicIPAddress doit aussi présenter une redondance avec la zone, et donc ne pas avoir de zone spécifiée.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Utilisez [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) pour obtenir l’adresse IP publique de la passerelle d’application. Copiez l’adresse IP publique ou le nom DNS, puis collez cette donnée dans la barre d’adresses de votre navigateur.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Supprimer des ressources
Explorez d’abord les ressources qui ont été créées avec la passerelle d’application puis, lorsque vous n’en avez plus besoin, utilisez la commande `Remove-AzureRmResourceGroup` pour supprimer le groupe de ressources, la passerelle d’application et toutes les ressources associées.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Utiliser une adresse IP virtuelle statique
> * Configurer le paramètre de configuration de mise à l’échelle automatique
> * Utiliser le paramètre de zone
> * Créer la passerelle Application Gateway

> [!div class="nextstepaction"]
> [Créer une passerelle d’application avec des règles d’acheminement par chemin d’URL](./tutorial-url-route-powershell.md)
