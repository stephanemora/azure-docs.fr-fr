---
title: 'Démarrage rapide : Créer un équilibreur de charge interne - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ce guide de démarrage rapide montre comment créer un équilibreur de charge interne en utilisant Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 15609435c7bc099d0ffe40759ea0f323b58a4545
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087547"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Démarrage rapide : Créer un équilibreur de charge interne pour équilibrer la charge de machines virtuelles avec Azure PowerShell

Découvrez comment démarrer avec Azure Load Balancer en utilisant Azure PowerShell pour créer un équilibreur de charge interne et deux machines virtuelles.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installé localement ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

* Nommé **myResourceGroupLB**.
* Dans l’emplacement **eastus**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Référence Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production. Pour plus d’informations sur les références SKU, consultez **[Références SKU Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Avant de déployer des machines virtuelles et de tester votre équilibreur de charge, créez les ressources de réseau virtuel associées.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Créer un réseau virtuel et un hôte Azure Bastion

Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) :

* Nommé **myVNet**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Sous-réseau nommé **myBackendSubnet**.
* Réseau virtuel **10.0.0.0/16**.
* Sous-réseau **10.0.0.0/24**.
* Sous-réseau nommé **AzureBastionSubnet**.
* Sous-réseau **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Créer une adresse IP publique pour l’hôte Azure Bastion

Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IP publique pour l’hôte bastion :

* Nommé **myPublicIPBastion**
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Méthode d’allocation **statique**.
* Référence **Standard**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Créer un hôte Azure Bastion

Utilisez [New-AzBastion](/powershell/module/az.network/new-azbastion) pour créer un hôte bastion :

* Nommé **myBastion**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Associée à une adresse IP publique **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Le déploiement de l’hôte Azure Bastion peut prendre quelques minutes.

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau
Créez un groupe de sécurité réseau pour définir les connexions entrantes vers votre réseau virtuel.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Créer une règle de groupe de sécurité réseau pour le port 80
Créez une règle de groupe de sécurité réseau avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) :

* Nommée **myNSGRuleHTTP**.
* Description de **Autoriser HTTP**.
* Accès **Autoriser**.
* Protocole **(*)** .
* Direction **entrante**.
* Priorité **2000**.
* Source **Internet**.
* Plage de ports sources de **(*)** .
* Préfixe d’adresse de destination de **(*)** .
* Port de destination **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un groupe de sécurité réseau avec [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) :

* Nommé **myNSG**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Avec les règles de sécurité créées dans les étapes précédentes stockées dans une variable.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Créer un équilibreur de charge standard

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :

  * Un pool d’adresses IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  * Un pool d’adresses IP principal auquel le pool frontal envoie le trafic réseau dont la charge est équilibrée.
  * Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  * Une règle d’équilibreur de charge qui définit la distribution du trafic aux machines virtuelles.

### <a name="create-frontend-ip"></a>Créer une adresse IP de serveur frontal

Créez une adresse IP front-end avec [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) :

* Nommée **myFrontEnd**.
* Adresse IP privée **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configurer un pool d’adresses back-end

Créez un pool d’adresses back-end avec [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) : 

* Nommé **myBackEndPool**.
* Les machines virtuelles se joignent à ce pool back-end dans les étapes suivantes.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre du trafic réseau. 

Une machine virtuelle dont le contrôle de sonde a échoué est supprimée de l’équilibreur de charge. La machine virtuelle est rajoutée à l’équilibreur de charge une fois l’échec résolu.

Créez une sonde d’intégrité avec [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) :

* Analyse l’intégrité des machines virtuelles.
* Nommée **myHealthProbe.** .
* Protocole **TCP**.
* Surveillant le **Port 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP frontale pour le trafic entrant.
* Le pool d’adresses IP principal qui reçoit le trafic.
* Les ports source et de destination requis. 

Créez une règle d’équilibreur de charge avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) : 

* Nommée **myHTTPRule**.
* Écoutant le **Port 80** dans le pool frontal **myFrontEnd**.
* Envoyant le trafic réseau dont la charge est équilibrée au pool d’adresses principal **myBackEndPool** à l’aide du **Port 80**. 
* Utilisant la sonde d’intégrité **myHealthProbe**.
* Protocole **TCP**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT
```
>[!NOTE]
>Avec cette configuration, les machines virtuelles du pool principal ne disposent pas d’une connectivité Internet sortante. </br> Pour plus d’informations sur la façon de fournir une connectivité sortante, consultez : </br> **[Connexions sortantes dans Azure](load-balancer-outbound-connections.md)**</br> Options pour fournir la connectivité : </br> **[Configuration de l’équilibreur de charge en sortie uniquement](egress-only.md)** </br> **[Qu’est-ce que le service NAT de Réseau virtuel ?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**


### <a name="create-load-balancer-resource"></a>Créer des ressources d’équilibreur de charge

Créez un équilibreur de charge interne avec [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) :

* Nommé **myLoadBalancer**.
* Dans l’emplacement **eastus**.
* Dans le groupe de ressources **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Création d’interfaces réseau

Créez trois interfaces réseau à l’aide de [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) :

#### <a name="vm-1"></a>Machine virtuelle 1

* Nommée **myNicVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.
* Attachée à l’équilibreur de charge **myLoadBalancer** dans **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>Machine virtuelle 2

* Nommée **myNicVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.
* Attachée à l’équilibreur de charge **myLoadBalancer** dans **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell
$cred = Get-Credential
```

Créez les machines virtuelles avec :

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>MV1

* Nommée **myVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM1**.
* Attachée à l’équilibreur de charge **myLoadBalancer**.
* Dans **Zone 1**.
* Dans l’emplacement **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>MV2

* Nommée **myVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM2**.
* Attachée à l’équilibreur de charge **myLoadBalancer**.
* Dans **Zone 2**.
* Dans l’emplacement **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production. Pour plus d’informations sur les références SKU, consultez **[Références SKU Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Avant de déployer des machines virtuelles et de tester votre équilibreur de charge, créez les ressources de réseau virtuel associées.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Créer un réseau virtuel et un hôte Azure Bastion

Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) :

* Nommé **myVNet**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Sous-réseau nommé **myBackendSubnet**.
* Réseau virtuel **10.0.0.0/16**.
* Sous-réseau **10.0.0.0/24**.
* Sous-réseau nommé **AzureBastionSubnet**.
* Sous-réseau **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Créer une adresse IP publique pour l’hôte Azure Bastion

Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IP publique pour l’hôte bastion :

* Nommé **myPublicIPBastion**
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Méthode d’allocation **statique**.
* Référence **Standard**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Créer un hôte Azure Bastion

Utilisez [New-AzBastion](/powershell/module/az.network/new-azbastion) pour créer un hôte bastion :

* Nommé **myBastion**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans le réseau virtuel **myVNet**.
* Associée à une adresse IP publique **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Le déploiement de l’hôte Azure Bastion peut prendre quelques minutes.


### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau
Créez un groupe de sécurité réseau pour définir les connexions entrantes vers votre réseau virtuel.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Créer une règle de groupe de sécurité réseau pour le port 80
Créez une règle de groupe de sécurité réseau avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) :

* Nommée **myNSGRuleHTTP**.
* Description de **Autoriser HTTP**.
* Accès **Autoriser**.
* Protocole **(*)** .
* Direction **entrante**.
* Priorité **2000**.
* Source **Internet**.
* Plage de ports sources de **(*)** .
* Préfixe d’adresse de destination de **(*)** .
* Port de destination **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un groupe de sécurité réseau avec [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) :

* Nommé **myNSG**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Avec les règles de sécurité créées dans les étapes précédentes stockées dans une variable.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Créer un équilibreur de charge de base

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :

  * Un pool d’adresses IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  * Un pool d’adresses IP principal auquel le pool frontal envoie le trafic réseau dont la charge est équilibrée.
  * Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  * Une règle d’équilibreur de charge qui définit la distribution du trafic aux machines virtuelles.

### <a name="create-frontend-ip"></a>Créer une adresse IP de serveur frontal

Créez une adresse IP front-end avec [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) :

* Nommée **myFrontEnd**.
* Adresse IP privée **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configurer un pool d’adresses back-end

Créez un pool d’adresses back-end avec [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) : 

* Nommé **myBackEndPool**.
* Les machines virtuelles se joignent à ce pool back-end dans les étapes suivantes.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre du trafic réseau. 

Une machine virtuelle dont le contrôle de sonde a échoué est supprimée de l’équilibreur de charge. La machine virtuelle est rajoutée à l’équilibreur de charge une fois l’échec résolu.

Créez une sonde d’intégrité avec [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) :

* Analyse l’intégrité des machines virtuelles.
* Nommée **myHealthProbe.** .
* Protocole **TCP**.
* Surveillant le **Port 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP frontale pour le trafic entrant.
* Le pool d’adresses IP principal qui reçoit le trafic.
* Les ports source et de destination requis. 

Créez une règle d’équilibreur de charge avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) : 

* Nommée **myHTTPRule**.
* Écoutant le **Port 80** dans le pool frontal **myFrontEnd**.
* Envoyant le trafic réseau dont la charge est équilibrée au pool d’adresses principal **myBackEndPool** à l’aide du **Port 80**. 
* Utilisant la sonde d’intégrité **myHealthProbe**.
* Protocole **TCP**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>Créer la ressource d’équilibreur de charge

Créez un équilibreur de charge public avec [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) :

* Nommé **myLoadBalancer**.
* Dans l’emplacement **eastus**.
* Dans le groupe de ressources **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Création d’interfaces réseau

Créez trois interfaces réseau à l’aide de [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) :

#### <a name="vm-1"></a>Machine virtuelle 1

* Nommée **myNicVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.
* Attachée à l’équilibreur de charge **myLoadBalancer** dans **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>Machine virtuelle 2

* Nommée **myNicVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.
* Attachée à l’équilibreur de charge **myLoadBalancer** dans **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Créer un groupe à haute disponibilité pour des machines virtuelles

Utilisez [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) pour créer un groupe à haute disponibilité pour les machines virtuelles :

* Nommé **myAvSet**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell
$cred = Get-Credential
```

Créez les machines virtuelles avec :

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>MV1

* Nommée **myVM1**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM1**.
* Attachée à l’équilibreur de charge **myLoadBalancer**.
* Dans l’emplacement **eastus**.
* Dans le groupe à haute disponibilité **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>MV2

* Nommée **myVM2**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicVM2**.
* Attachée à l’équilibreur de charge **myLoadBalancer**.
* Dans l’emplacement **eastus**.
* Dans le groupe à haute disponibilité **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

La création et la configuration des trois machines virtuelles prennent quelques minutes.

---

## <a name="install-iis"></a>Installer IIS

Utilisez [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension?view=latest) pour installer l’extension de script personnalisé. 

L’extension exécute PowerShell Add-WindowsFeature Web-Server pour installer le serveur web IIS, puis met à jour la page Default.htm pour qu’elle affiche le nom d’hôte de la machine virtuelle :

### <a name="vm1"></a>MV1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>MV2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

### <a name="create-network-interface"></a>Créer une interface réseau

Créez une interface réseau avec [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) :

#### <a name="mytestvm"></a>myTestVM

* Nommée **myNicTestVM**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Dans l’emplacement **eastus**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Créer une machine virtuelle

Définissez un nom d’utilisateur administrateur et un mot de passe pour la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell
$cred = Get-Credential
```

Créez la machine virtuelle avec :

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* Nommé **myTestVM**.
* Dans le groupe de ressources **myResourceGroupLB**.
* Attachée à l’interface réseau **myNicTestVM**.
* Dans l’emplacement **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Test

1. [Connectez-vous](https://portal.azure.com) au portail Azure.

1. Recherchez l’adresse IP privée de l’équilibreur de charge sur l’écran **Vue d’ensemble**. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis sélectionnez **myLoadBalancer**.

2. Notez ou copiez l’adresse en regard de **Adresse IP privée** dans la **Vue d’ensemble** de **myLoadBalancer**.

3. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources** puis, dans la liste des ressources, sélectionnez **myTestVM** qui se trouve dans le groupe de ressources **myResourceGroupLB**.

4. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

6. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

7. Ouvrez **Internet Explorer** sur **myTestVM**.

8. Entrez l’adresse IP de l’étape précédente dans la barre d’adresse du navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Créer un équilibreur de charge interne standard" border="true":::
   
Pour voir l’équilibreur de charge répartir le trafic entre les trois machines virtuelles, vous pouvez personnaliser la page par défaut du serveur web IIS de chaque machine virtuelle, puis forcer votre navigateur à s’actualiser à partir de la machine cliente.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n'en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, l'équilibreur de charge et les ressources restantes.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide

* Vous avez créé un équilibreur de charge interne standard ou de base
* Attaché des machines virtuelles. 
* Vous avez configuré la règle de trafic et la sonde d’intégrité de l’équilibreur de charge.
* Vous avez testé l’équilibreur de charge.

Pour en savoir plus sur Azure Load Balancer, consultez [Qu’est-ce qu’Azure Load Balancer ?](load-balancer-overview.md) et [Questions fréquentes sur Load Balancer](load-balancer-faqs.md).

* Découvrez-en plus sur les [équilibreurs de charge et les zones de disponibilité](load-balancer-standard-availability-zones.md).


