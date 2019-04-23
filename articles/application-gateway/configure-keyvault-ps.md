---
title: Configurez la terminaison SSL avec certificats de Key Vault à l’aide d’Azure PowerShell
description: Découvrez comment vous pouvez intégrer passerelle d’application Azure Key Vault pour les certificats de serveur qui sont attachés aux écouteurs de protocole HTTPS est activé.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/22/2019
ms.author: victorh
ms.openlocfilehash: 62f3038957d3e6af02bbdbb80fd69757621fc494
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148451"
---
# <a name="configure-ssl-termination-with-key-vault-certificates-using-azure-powershell"></a>Configurez la terminaison SSL avec certificats de Key Vault à l’aide d’Azure PowerShell

[Azure Key Vault](../key-vault/key-vault-whatis.md) est un magasin des secrets gérés par la plateforme que vous pouvez utiliser pour protéger les secrets, les clés et certificats SSL. Application Gateway prend en charge l’intégration avec Key Vault (en préversion publique) pour les certificats de serveur qui sont attachés aux écouteurs de protocole HTTPS est activé. Cette prise en charge est limitée à la référence (SKU) v2 d’Application Gateway.

Pour plus d’informations, consultez [une terminaison SSL avec certificats Key Vault](key-vault-certs.md).

Cet article vous montre un script Azure PowerShell pour intégrer le coffre de clés avec Application Gateway de certificats d’arrêt SSL.

> [!IMPORTANT]
> L’intégration d’Application Gateway Key Vault est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Cet article nécessite le module Azure PowerShell version 1.0.0 ou une version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Pour exécuter les commandes dans cet article, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="prerequisites"></a>Conditions préalables

Vous devez disposer du module ManagedServiceIdentity installé avant de commencer.

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>Exemple de script

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "TestKeyVaultAppGw"
$appgwName = "AppGwKVIntegration"

#Create Resource Group 
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location

#Create User Managed Identity
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname

#Create Key Vault, policy and certificate to be used by Application Gateway
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")


#Create Application Gateway with HTTPS listener attached to Key Vault and an HTTP listener
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)

#Application Gateway v2 Static public VIP
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard

$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80

#point ssl certificate to key vault
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId

$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2

#assign user managed identity to Application Gateway
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id

$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la terminaison SSL](ssl-overview.md).