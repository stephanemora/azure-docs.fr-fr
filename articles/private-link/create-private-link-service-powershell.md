---
title: Créer un service Azure Private Link à l’aide d’Azure PowerShell | Microsoft Docs
description: Découvrez comment créer un service Azure Private Link à l’aide d’Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: c4395628ac31dd69a4978f7f68ecc24ca1e15cfb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453126"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Créer un service Private Link à l’aide d’Azure PowerShell
Cet article explique comment créer un service Private Link dans Azure à l’aide d’Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter la dernière version du module Azure PowerShell pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de créer votre liaison privée, vous devez créer un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *WestCentralUS* :

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez un réseau virtuel pour votre Private Link avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé *myvnet* avec un sous réseau pour le frontal (*frontendSubnet*), le frontal (*backendSubnet*) et Private Link (*otherSubnet*) :

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Créer un équilibrage de charge interne
Créez un équilibreur de charge Standard Load Balancer interne avec [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). L’exemple suivant crée un Standard Load Balancer interne à l’aide de la configuration IP frontale, de la sonde, de la règle et du pool principal que vous avez créés dans les étapes précédentes :

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Créer un service Private Link
Créez un service Private Link avec [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Cet exemple crée un service Private Link nommé *myPLS* en utilisant le Standard Load Balancer dans le groupe de ressources nommé *myResourceGroup*. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Obtenir le service Private Link
Pour obtenir des détails sur votre service de liaison privée [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice), effectuez les étapes suivantes :

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

À ce stade, votre service Private Link est correctement créé et prêt à recevoir le trafic. Notez que l’exemple ci-dessus illustre uniquement la création d’un service Private Link à l’aide d’Azure PowerShell.  Nous n’avons pas configuré les pools principaux de l’équilibreur de charge ou une application sur les pools principaux pour écouter le trafic. Si vous souhaitez consulter des flux de trafic de bout en bout, nous vous conseillons vivement de configurer votre application derrière votre Standard Load Balancer. 

Nous allons ensuite montrer comment mapper ce service à une instance Private Endpoint dans un réseau virtuel différent à l’aide de PowerShell. Là encore, l’exemple est limité à la création de Private Endpoint et à la connexion au service Private Link créé ci-dessus. Vous pouvez créer des machines virtuelles dans le réseau virtuel pour envoyer/recevoir le trafic vers Private Endpoint pour votre scénario. 

## <a name="create-a-private-endpoint"></a>Créer un point de terminaison privé
### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez un réseau virtuel pour votre Private Endpoint avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Cet exemple crée un réseau virtuel nommé *vnetPE* dans le groupe de ressources nommé *myResourceGroup* :
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Créer un Private Endpoint
Créez un Private Endpoint pour l’utilisation du service Private Link créé ci-dessus dans votre réseau virtuel :
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Récupérer le point de terminaison privé
Récupérez l’adresse IP du point de terminaison privé avec `Get-AzPrivateEndpoint` comme suit :

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Approuver la connexion Private Endpoint
Approuvez la connexion du point de terminaison privé au service Private Link avec « Approve-AzPrivateEndpointConnection ».

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Azure Private Link](private-link-overview.md)
 
