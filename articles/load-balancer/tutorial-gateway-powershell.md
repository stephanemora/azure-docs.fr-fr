---
title: 'Tutoriel : Créer un équilibreur de charge de passerelle - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Utilisez ce tutoriel pour apprendre à créer un équilibreur de charge de passerelle à l’aide d’Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 97b8b428d71da312c7cfc0ed9da057003ac68ee7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097578"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-azure-powershell"></a>Tutoriel : Créer un équilibreur de charge de passerelle à l’aide d’Azure PowerShell

Azure Load Balancer se compose de SKUs Standard, Basic et Gateway. L'équilibreur de charge de passerelle est utilisé pour l'insertion transparente d'appareils virtuels de réseau (NVA)(appliance virtuelle réseau). Utilisez Load Balancer de passerelle pour les scénarios qui requièrent des performances élevées et une haute évolutivité de Appliances virtuelles réseau.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Inscrire la fonctionnalité de préversion
> * Créez un réseau virtuel.
> * Créer un groupe de sécurité réseau.
> * Créez un équilibreur de charge de passerelle.
> * Chaînez un serveur frontal de l’équilibreur de charge à l’équilibreur de charge de la passerelle.

> [!IMPORTANT]
> Gateway Azure Load Balancer est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

- Un compte Azure avec un abonnement actif : [créez gratuitement un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une référence SKU standard publique existante Azure Load Balancer. Pour plus d'informations sur la création d'un équilibreur de charge, voir **[Créer un équilibreur de charge public à l'aide d’Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)** .
    - Dans le cadre de ce tutoriel, l’équilibreur de charge existant dans les exemples est nommé **myLoadBalancer**.
- Azure PowerShell installé localement ou Azure Cloud Shell

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="register-preview-feature"></a>Inscrire la fonctionnalité d’évaluation

Dans le cadre de la préversion publique de l’équilibreur de charge de la passerelle, le fournisseur doit être inscrit dans votre abonnement Azure.

Utilisez [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) pour enregistrer la fonctionnalité du fournisseur **AllowGatewayLoadBalancer** :

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

Utilisez [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) pour enregistrer le fournisseur de ressources **Microsoft.Network** :

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorGwLB-rg' -Location 'eastus'

```

## <a name="create-virtual-network"></a>Création d’un réseau virtuel

Un réseau virtuel est nécessaire pour les ressources qui se trouvent dans le pool backend de l'équilibreur de charge de la passerelle. Créez le réseau virtuel à l’aide de la commande [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Utilisez [New-AzBastion](/powershell/module/az.network/new-azbastion) pour déployer un hôte bastion pour une gestion sécurisée des ressources dans le réseau virtuel.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="create-nsg"></a>Création d’un groupe de sécurité réseau

L’exemple suivant permet de créer un groupe de sécurité réseau nommé : Vous allez configurer les règles NSG nécessaires pour le trafic réseau dans le réseau virtuel créé précédemment.

Utilisez [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) pour créer des règles pour le groupe de sécurité réseau. Utilisez [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) pour créer le groupe de sécurité réseau.

```azurepowershell-interactive
## Create rule for network security group and place in variable. ##
$nsgrule1 = @{
    Name = 'myNSGRule-AllowAll'
    Description = 'Allow all'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule1

$nsgrule2 = @{
    Name = 'myNSGRule-AllowAll-TCP-Out'
    Description = 'Allow all TCP Out'
    Protocol = 'TCP'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Outbound'
}
$rule2 = New-AzNetworkSecurityRuleConfig @nsgrule2

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    SecurityRules = $rule1,$rule2
}
New-AzNetworkSecurityGroup @nsg
```

## <a name="create-gateway-load-balancer"></a>Créez un équilibreur de charge de passerelle.

Dans cette section, vous allez créer la configuration et déployer l’équilibrage de charge de la passerelle. Créez une configuration d’adresse IP front-end pour l’équilibreur de charge en utilisant [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). 

Vous allez utiliser [New-AzLoadBalancerTunnelInterface](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) pour créer deux interfaces de tunnel pour l’équilibreur de charge. 

Créez un pool d’adresses principales avec [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) pour les NVAS. 

Une sonde d’intégrité est nécessaire pour surveiller l’intégrité des instances de serveur principal dans l’équilibreur de charge. Utilisez [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) pour créer les sondes d’intégrité.

Le trafic destiné aux instances principales est routé avec une règle d’équilibrage de charge. Utilisez [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) pour créer la configuration d’équilibrage de charge.

Utilisez [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) pour créer le déploiement de l’équilibrage de charge.

```azurepowershell-interactive
## Place virtual network configuration in a variable for later use. ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
}
$vnet = Get-AzVirtualNetwork @net

## Create load balancer frontend configuration and place in variable. ## 
$fe = @{
    Name = 'myFrontend'
    SubnetId = $vnet.subnets[0].id
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create backend address pool configuration and place in variable. ## 
$int1 = @{
    Type = 'Internal'
    Protocol = 'Vxlan'
    Identifier = '800'
    Port = '10800'
}
$tunnelInterface1 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int1

$int2 = @{
    Type = 'External'
    Protocol = 'Vxlan'
    Identifier = '801'
    Port = '10801'
}
$tunnelInterface2 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int2

$pool = @{
    Name = 'myBackendPool'
    TunnelInterface = $tunnelInterface1,$tunnelInterface2
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @pool

## Create the health probe and place in variable. ## 
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ## 
$para = @{
    Name = 'myLBRule'
    Protocol = 'All'
    FrontendPort = '0'
    BackendPort = '0'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
$rule = New-AzLoadBalancerRuleConfig @para

## Create the load balancer resource. ## 
$lb = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
    Location = 'eastus'
    Sku = 'Gateway'
    LoadBalancingRule = $rule
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
New-AzLoadBalancer @lb

```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Ajouter des appliances virtuelles réseau à la passerelle Load Balancer pool backend
Déployez Appliances virtuelles réseau via la place de marché Azure. Une fois le déploiement terminé, ajoutez les machines virtuelles au pool principal avec [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Chaînez un serveur frontal de l’équilibreur de charge à l’équilibreur de charge de la passerelle

Dans cet exemple, vous allez chaîner le serveur frontal d’un équilibreur de charge standard à l’équilibreur de charge de la passerelle. 

Vous allez ajouter le serveur frontal à l’adresse IP frontale d’un équilibreur de charge existant dans votre abonnement.

Utilisez [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) pour chaîner le serveur frontal de l’équilibreur de charge de la passerelle à votre équilibreur de charge existant.

```azurepowershell-interactive
## Place the gateway load balancer configuration into a variable. ##
$par1 = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
}
$gwlb = Get-AzLoadBalancer @par1

## Place the existing load balancer into a variable. ##
$par2 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @par2

## Place the existing public IP for the existing load balancer into a variable.
$par3 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}
$publicIP = Get-AzPublicIPAddress @par3

## Chain the gateway load balancer to your existing load balancer frontend. ##
$par4 = @{
    Name = 'myFrontEndIP'
    PublicIPAddress = $publicIP
    LoadBalancer = $lb
    GatewayLoadBalancerId = $gwlb.FrontendIpConfigurations.Id
}
$config = Set-AzLoadBalancerFrontendIpConfig @par4

$config | Set-AzLoadBalancer

```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n'en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, l'équilibreur de charge et les ressources restantes.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorGwLB-rg'
```

## <a name="next-steps"></a>Étapes suivantes

Créer des appliances virtuelles réseau dans Azure. 

Lorsque vous créez le Appliances virtuelles réseau, choisissez les ressources créées dans ce tutoriel :

* Réseau virtuel

* Subnet

* Groupe de sécurité réseau

* Équilibreur de charge de passerelle

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge Azure interrégional :
> [!div class="nextstepaction"]
> [Équilibreur de charge inter-région](tutorial-cross-region-powershell.md)
