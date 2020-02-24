---
title: 'Démarrage rapide : Créer un équilibreur de charge - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ce guide de démarrage rapide explique comment créer un équilibreur de charge à l’aide d’Azure PowerShell.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 0cd2bb54bb436beaa933195b88bc6f13a1b23e6f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470413"
---
# <a name="quickstart-create-a-load-balancer-using-azure-powershell"></a>Démarrage rapide : Créer un équilibreur de charge à l’aide d’Azure PowerShell

Ce démarrage rapide vous montre comment créer un Standard Load Balancer à l’aide d’Azure PowerShell. Pour tester l’équilibreur de charge, déployez trois machines virtuelles exécutant un serveur Windows, puis équilibrez la charge d’une application web entre les machines virtuelles. Pour plus d’informations sur l’équilibreur de charge standard, consultez [Qu’st-ce que Standard Load Balancer](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de créer votre équilibreur de charge, vous devez créer un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupSLB* à l’emplacement *EastUS* :

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Pour accéder à votre application sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. Créez une adresse IP publique avec [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). L’exemple suivant crée une adresse IP publique redondante interzone nommée *myPublicIP* dans le groupe de ressources *myResourceGroupSLB* :

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

Pour créer une adresse IP publique zonale dans la zone 1, utilisez ce qui suit :

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
 -zone 1
```

Utilisez ```-SKU Basic``` pour créer une adresse IP publique de base. Les adresses IP publiques de base ne sont pas compatibles avec l’équilibreur de charge **Standard**. Microsoft recommande d’utiliser le type **Standard** pour les charges de travail de production.

> [!IMPORTANT]
> Le reste de ce guide de démarrage rapide suppose que la référence SKU **Standard** est choisie pendant le processus de sélection de SKU ci-dessus.

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous allez établir la configuration IP front-end et configurer le pool d’adresses back-end pour l’équilibreur de charge, puis créer l’équilibreur de charge Standard Load Balancer.

### <a name="create-frontend-ip"></a>Créer une adresse IP de serveur frontal

Créez une configuration IP front-end avec [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). L’exemple suivant crée une configuration IP front-end nommée *myFrontEnd* et joint l’adresse *myPublicIP* :

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Configurer un pool d’adresses back-end

Créez un pool d’adresses back-end avec [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Les machines virtuelles se joignent à ce pool back-end dans les étapes suivantes. L’exemple suivant crée un pool d’adresses back-end nommé *myBackEndPool* :

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité
Pour permettre à l’équilibrage de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Par défaut, une machine virtuelle est supprimée de la distribution d’équilibrage de charge après deux échecs consécutifs à des intervalles de 15 secondes. Vous créez une sonde d’intégrité selon un protocole ou une page de vérification d’intégrité spécifique pour votre application.

L’exemple suivant permet de créer une sonde TCP. Vous pouvez également créer des sondes HTTP personnalisées pour des contrôles d’intégrité plus affinés. Lorsque vous utilisez une sonde HTTP personnalisée, vous devez créer la page de contrôle d’intégrité, par exemple *healthcheck.aspx*. La sonde doit retourner une réponse **HTTP 200 OK** pour l’équilibreur de charge pour assurer la rotation de l’hôte.

Pour créer une sonde d’intégrité TCP, utilisez [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). L’exemple suivant crée une sonde d’intégrité nommée *myHealthProbe* qui surveille chaque machine virtuelle sur le port *HTTP* *80* :

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge
Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Pour veiller à ce que seules les machines virtuelles saines reçoivent le trafic, vous devez également définir la sonde d’intégrité à utiliser.

Créez une règle d’équilibreur de charge avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). L’exemple suivant crée une règle d’équilibreur de charge nommée *myLoadBalancerRule* et équilibre le trafic sur le port *TCP* *80* :

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>Créer les règles NAT

Créez les règles NAT à l’aide de [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). L’exemple suivant crée des règles NAT nommées *myLoadBalancerRDP1* et *myLoadBalancerRDP2* pour autoriser les connexions RDP aux serveurs back-end avec les ports 4221 et 4222 :

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

Créez l’équilibreur de charge Standard Load Balancer avec [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). L’exemple suivant crée un Standard Load Balancer public nommé myLoadBalancer en utilisant la configuration IP front-end, le pool back-end, la sonde d’intégrité, la règle d’équilibrage de charge et les règles NAT que vous avez créés dans les étapes précédentes :

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

Utilisez ```-SKU Basic``` pour créer un équilibreur de charge de base. Microsoft recommande d’utiliser le type Standard pour les charges de travail de production.

> [!IMPORTANT]
> Le reste de ce guide de démarrage rapide suppose que la référence SKU **Standard** est choisie pendant le processus de sélection de SKU ci-dessus.

## <a name="create-network-resources"></a>Créer des ressources réseau
Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur, vous devez créer des ressources de réseau virtuel de prise en charge (réseau virtuel et cartes réseau virtuelles). 

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* :

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Créer des adresses IP publiques pour les machines virtuelles

Pour accéder à vos machines virtuelles à l’aide d’une connexion RDP, vous avez besoin d’adresses IP publiques pour les machines virtuelles. Dans la mesure où un équilibreur de charge standard est utilisé dans ce scénario, vous devez créer des adresses IP publiques standard pour les machines virtuelles avec [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```

Utilisez ```-SKU Basic``` pour créer des adresses IP publiques de base. Microsoft recommande d’utiliser le type Standard pour les charges de travail de production.

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau
Créez un groupe de sécurité réseau pour définir les connexions entrantes vers votre réseau virtuel.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Créer une règle de groupe de sécurité réseau pour le port 3389
Créez une règle de groupe de sécurité réseau pour autoriser les connexions RDP via le port 3389 avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Créer une règle de groupe de sécurité réseau pour le port 80
Créez une règle de groupe de sécurité réseau pour autoriser les connexions entrantes via le port 80 avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un groupe de sécurité réseau avec [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Créer des cartes réseau
Créez des cartes réseau virtuelles et associez-les aux adresses IP publiques et aux groupes de sécurité réseau créés au cours des étapes précédentes avec [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). L’exemple suivant crée trois cartes réseau virtuelles. (Une carte d’interface réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes). Vous pouvez créer des machines virtuelles et des cartes d’interface réseau virtuelles supplémentaires à tout moment et les ajouter à l’équilibreur de charge :

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell
$cred = Get-Credential
```

Vous pouvez maintenant créer les machines virtuelles avec [New-AzVM](/powershell/module/az.compute/new-azvm). L’exemple suivant crée deux machines virtuelles ainsi que les composants de réseau virtuel nécessaires s’ils n’existent pas encore. Dans cet exemple, les cartes réseau (*MyNic1*, *MyNic2* et *MyNic3*) créées à l’étape précédente sont affectées aux machines virtuelles *myVM1*, *myVM2* et *VM3*. Par ailleurs, comme les cartes réseau sont associées au pool back-end de l’équilibreur de charge, les machines virtuelles sont automatiquement ajoutées au pool back-end.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

La création et la configuration des trois machines virtuelles prennent quelques minutes.

### <a name="install-iis-with-a-custom-web-page"></a>Installer IIS avec une page web personnalisée

Installez IIS avec une page web personnalisée sur les deux machines virtuelles back-end comme suit :

1. Obtenez les adresses IP publiques des trois machines virtuelles avec `Get-AzPublicIPAddress`.

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Créez des connexions Bureau à distance avec *myVM1*, *myVM2* et *myVM3* en utilisant les adresses IP publiques des machines virtuelles comme suit : 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Entrez les informations d’identification de chaque machine virtuelle pour démarrer la session RDP.
4. Lancez Windows PowerShell sur chaque machine virtuelle et utilisez les commandes suivantes pour installer le serveur IIS et mettre à jour le fichier htm par défaut.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Fermez les connexions RDP à *myVM1*, *myVM2* et *myVM3*.


## <a name="test-load-balancer"></a>Tester l’équilibreur de charge
Obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). L’exemple suivant obtient l’adresse IP pour *myPublicIP* créée précédemment :

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

Vous pouvez alors entrer l’adresse IP publique dans un navigateur web. Le site web s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibreur de charge a distribué le trafic comme dans l’exemple suivant :

![Tester l’équilibreur de charge](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Pour visualiser la distribution de trafic par l’équilibreur de charge sur les trois machines virtuelles exécutant votre application, vous pouvez forcer l’actualisation de votre navigateur web. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un Standard Load Balancer, associé des machines virtuelles à celui-ci, configuré la règle de trafic d’équilibreur de charge, la sonde d’intégrité, puis testé Load Balancer. Pour en savoir plus sur Azure Load Balancer, consultez les [tutoriels Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Découvrez-en plus sur les [équilibreurs de charge et les zones de disponibilité](load-balancer-standard-availability-zones.md).