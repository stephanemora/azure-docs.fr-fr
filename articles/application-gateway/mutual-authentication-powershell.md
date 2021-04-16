---
title: Configurer l’authentification mutuelle sur Azure Application Gateway via PowerShell
description: Apprenez à configurer une instance d’Application Gateway pour bénéficier d’une authentification mutuelle via PowerShell
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230860"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Configurer l’authentification mutuelle sur Application Gateway via PowerShell (préversion)
Cet article explique comment utiliser PowerShell pour configurer l’authentification mutuelle sur votre instance d’Application Gateway. L’authentification mutuelle signifie qu’Application Gateway authentifie le client qui envoie la requête à l’aide du certificat client que vous chargez sur Application Gateway. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cet article nécessite l’utilisation du module Azure PowerShell version 1.0.0 ou ultérieure. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Login-AzAccount` pour créer une connexion avec Azure.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer l’authentification mutuelle sur une instance d’Application Gateway, un certificat client doit être chargé sur la passerelle. Le certificat client sera utilisé pour valider le certificat que le client présentera à Application Gateway. Dans le cadre d’un test, vous pouvez utiliser un certificat auto-signé. Toutefois, ces derniers ne sont pas recommandés pour les charges de travail de production, car elles sont plus difficiles à gérer et ne sont pas entièrement sécurisées.

Pour en savoir plus, en particulier sur le type de certificat client que vous pouvez charger, consultez [Présentation de l’authentification mutuelle sur Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Commencez par créer un groupe de ressources Azure dans votre abonnement. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Déployez un réseau virtuel pour y déployer votre instance d’Application Gateway.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Créez une adresse IP publique.

Créez une adresse IP publique à utiliser avec votre instance d’Application Gateway. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Créer la configuration IP d’Application Gateway

Créez les configurations IP et le port front-end. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Configurer le protocole SSL front-end 

Configurez les certificats SSL pour votre instance d’Application Gateway.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Configurer une authentification de client 

Configurez l’authentification de client sur votre instance d’Application Gateway. Pour plus d’informations sur l’extraction de chaînes de certificats d’autorité de certification clients approuvés, consultez l’article [Exporter une chaîne de certificats d’autorité de certification clients approuvés à utiliser avec l’authentification client](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Veillez à charger l’intégralité de la chaîne de certificats d’autorité de certification clients dans un même fichier, et une seule chaîne par fichier.  

> [!NOTE]
> Nous vous recommandons d’utiliser TLS 1.2 avec l’authentification mutuelle, car TLS 1.2 sera obligatoire à l’avenir. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>Configurer le pool back-end et les paramètres

Configurez le pool back-end et les paramètres de votre instance d’Application Gateway. Éventuellement, configurez le certificat racine approuvé du serveur back-end pour le chiffrement SSL de bout en bout.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>Configurer la règle

Configurez une règle sur votre instance d’Application Gateway.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Configurer une stratégie SSL par défaut pour les écouteurs ultérieurs

Vous avez configuré une stratégie SSL spécifique à un écouteur lors de la configuration de l’authentification mutuelle. Dans cette étape, vous pouvez éventuellement définir la stratégie SSL par défaut pour les écouteurs à venir que vous créez. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

En vous basant sur tout ce que nous avons créé ci-dessus, déployez votre instance d’Application Gateway.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle d’application et toutes les ressources associées à l’aide de [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Renouveler les certificats d’autorité de certification clients qui ont expiré

Si votre certificat d’autorité de certification client a expiré, vous pouvez le mettre à jour sur votre passerelle en procédant comme suit : 

1. Connexion à Azure
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Obtenir votre configuration Application Gateway
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Supprimer le certificat client approuvé de la passerelle 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Ajouter le nouveau certificat sur la passerelle 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Mettre à jour la passerelle avec le nouveau certificat 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)