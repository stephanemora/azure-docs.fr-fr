---
title: 'Tutoriel : Créer une passerelle d’application redondante dans une zone, avec une mise à l’échelle automatique et avec une adresse IP réservée - Azure PowerShell'
description: Dans ce tutoriel, apprenez à créer une passerelle d'application redondante dans une zone, avec une mise à l'échelle automatique et avec une adresse IP réservée à l’aide d'Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99fa5d6f0ba74b56a53f2d1af1b99c7e5c2896a7
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323198"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Tutoriel : Créer une passerelle d'application qui améliore l'accès aux applications web

Si vous êtes un administrateur informatique soucieux d'améliorer l'accès aux applications web, vous pouvez optimiser votre passerelle d'application pour qu'elle s'adapte à la demande des clients et s'étende sur plusieurs zones de disponibilité. Ce tutoriel vous aide à configurer les fonctionnalités Azure Application Gateway qui gèrent la mise à l'échelle automatique, la redondance de zone et les adresses IP virtuelles réservées (adresses IP statiques). Vous allez utiliser des cmdlets Azure PowerShell et le modèle de déploiement Azure Resource Manager pour résoudre le problème.

> [!IMPORTANT] 
> La référence SKU de la passerelle d’application redondante interzone et avec mise à l’échelle automatique est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel avec mise à l'échelle automatique
> * Créer une adresse IP publique réservée
> * Configurer l'infrastructure de votre passerelle d'application
> * Spécifier la mise à l’échelle automatique
> * Créer la passerelle Application Gateway
> * Tester la passerelle d’application

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour ce didacticiel, vous devez exécuter Azure PowerShell localement. Vous devez disposer du module Azure PowerShell version 6.9.0 ou d’une version ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Après avoir vérifié la version PowerShell, exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

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

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel composé d'un sous-réseau dédié pour une passerelle d'application avec mise à l'échelle automatique. Actuellement, une seule passerelle d’application de mise à l’échelle automatique peut être déployée dans chaque sous-réseau dédié.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Créer une adresse IP publique réservée

Définissez la méthode d'allocation d'adresses PublicIPAddress sur **Statique**. Une adresse IP virtuelle de passerelle d’application de mise à l’échelle automatique peut être uniquement statique. Les adresses IP dynamiques ne sont pas prises en charge. Seule la référence SKU PublicIpAddress standard est prise en charge.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Récupérer les détails

Récupérez les détails du groupe de ressources, du sous-réseau et de l'adresse IP dans un objet local afin de créer des informations de configuration d'adresse IP pour la passerelle d'application.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurer l’infrastructure

Configurez l'adresse IP, l'adresse IP frontend, le pool principal, les paramètres HTTP, le certificat, le port, l'écouteur et la règle dans un format identique à celui de la passerelle d'application standard existante. La nouvelle référence SKU suit le même modèle d’objet que la référence SKU Standard.

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

Vous pouvez maintenant spécifier la configuration de mise à l'échelle automatique pour la passerelle d'application. Deux types de configuration de mise à l’échelle automatique sont pris en charge :

* **Mode de capacité fixe**. Dans ce mode, la passerelle d’application n’effectue pas de mise à l’échelle automatique et fonctionne à une capacité d’unité d’échelle fixe.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Mode de mise à l’échelle automatique**. Dans ce mode, la passerelle d’application se met automatiquement à l’échelle en fonction du modèle de trafic d’application.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Créez la passerelle d'application et incluez les zones de redondance et la configuration de mise à l'échelle automatique.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Utilisez Get-AzureRmPublicIPAddress pour obtenir l'adresse IP publique de la passerelle d'application. Copiez l’adresse IP publique ou le nom DNS, puis collez cette donnée dans la barre d’adresses de votre navigateur.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Supprimer des ressources

Commencez par explorer les ressources créées avec la passerelle d'application. Puis, lorsque vous n'en aurez plus besoin, vous pourrez utiliser la commande `Remove-AzureRmResourceGroup` pour supprimer le groupe de ressources, la passerelle d'application et toutes les ressources associées.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une passerelle d’application avec des règles d’acheminement par chemin d’URL](./tutorial-url-route-powershell.md)
