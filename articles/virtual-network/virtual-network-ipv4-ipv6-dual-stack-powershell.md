---
title: Déployer une application de double pile IPv6 dans un réseau virtuel Azure - PowerShell
titlesuffix: Azure Virtual Network
description: Cet article montre comment déployer une application de double pile IPv6 dans un réseau virtuel Azure à l’aide d’Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: f26391e36e3208996160fffad01e39ec2f182318
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130896"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Déployer une application de double pile IPv6 dans Azure - PowerShell (version préliminaire)

Cet article vous montre comment déployer une application de double pile (IPv4 + IPv6) dans Azure, qui inclut un réseau virtuel de pile double et un sous-réseau, un équilibreur de charge avec les configurations frontales double (IPv4 + IPv6), les machines virtuelles avec des cartes réseau qui ont une configuration IP double, réseau groupe de sécurité et les adresses IP publiques.

> [!Important]
> Prise en charge IPv6 pour le réseau virtuel Azure est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et utiliser PowerShell en local, cet article nécessite le module Azure PowerShell version 6.9.0 ou version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="prerequisites"></a>Conditions préalables
Avant de déployer une application de pile double dans Azure, vous devez configurer votre abonnement pour cette fonctionnalité en version préliminaire à l’aide de la commande Azure PowerShell suivante :

Enregistrer comme suit :
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Vous pouvez vérifier votre statut d’enregistrement en exécutant la commande Azure PowerShell suivante : Vérifier l’inscription comme suit :
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
À l’issue de l’installation, exécutez la commande suivante :

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer votre réseau virtuel double pile, vous devez créer un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myRGDualStack* dans le *east us* emplacement :

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Créer des adresses IP publiques IPv4 et IPv6
Pour accéder à vos machines virtuelles à partir d’Internet, vous avez besoin d’adresses IP publiques IPv4 et IPv6 pour l’équilibrage de charge. Créer des adresses IP publiques avec [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). L’exemple suivant crée IPv4 et IPv6 adresse IP publique nommée *dsPublicIP_v4* et *dsPublicIP_v6* dans le *dsRG1* groupe de ressources :

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Pour accéder à vos machines virtuelles à l’aide d’une connexion RDP, créez une adresses IP publiques IPV4 pour les machines virtuelles avec [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Créer un équilibreur de charge de base

Dans cette section, vous configurez double frontend IP (IPv4 et IPv6) et le pool d’adresses back-end pour l’équilibrage de charge, puis créez un équilibreur de charge de base.

### <a name="create-front-end-ip"></a>Créer une configuration IP front-end

Créez une configuration IP front-end avec [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). L’exemple suivant crée frontal IPv4 et IPv6 configurations IP nommées *dsLbFrontEnd_v4* et *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Configurer un pool d’adresses back-end

Créez un pool d’adresses back-end avec [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Les machines virtuelles se joignent à ce pool back-end dans les étapes suivantes. L’exemple suivant crée des pools d’adresses back-end nommés *dsLbBackEndPool_v4* et *dsLbBackEndPool_v6* pour inclure des machines virtuelles avec des configurations IPV4 et IPv6 carte d’interface réseau :

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Pour rendre bien sûr uniquement intègre machines virtuelles recevoir le trafic, vous pouvez éventuellement définir une sonde d’intégrité. Équilibreur de charge de base utilise une sonde IPv4 pour évaluer l’intégrité des points de terminaison IPv4 et IPv6 sur les machines virtuelles. Équilibreur de charge standard prend en charge explicitement les sondes d’intégrité IPv6.

Créez une règle d’équilibreur de charge avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). L’exemple suivant crée des règles d’équilibrage de charge nommés *dsLBrule_v4* et *dsLBrule_v6* et équilibre le trafic sur *TCP* port *80* à configurations IP frontales IPv4 et IPv6 :

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

Créez l’équilibreur de charge Basic Load Balancer avec [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). L’exemple suivant crée une base équilibreur de charge public nommé *myLoadBalancer* les configurations d’IP frontales IPv4 et IPv6, pools principaux, sondes d’intégrité, l’équilibrage de charge des règles d’et NAT des règles que vous avez créé dans le étapes précédentes :

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Créer des ressources réseau
Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur, vous devez créer les ressources réseau prise en charge - haute disponibilité, de groupe de sécurité réseau, de réseau virtuel et de cartes réseau virtuelles. 
### <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité
Pour améliorer la haute disponibilité de votre application, placez vos machines virtuelles dans un groupe à haute disponibilité.

Créez un groupe à haute disponibilité avec la commande [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). L’exemple suivant permet de créer un groupe à haute disponibilité nommé *myAvailabilitySet* :

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau

Créer un groupe de sécurité réseau pour les règles qui gouvernent les communications entrantes et sortantes de votre réseau virtuel.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Créer une règle de groupe de sécurité réseau pour le port 3389

Créez une règle de groupe de sécurité réseau pour autoriser les connexions RDP via le port 3389 avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Créer une règle de groupe de sécurité réseau pour le port 80

Créer une règle de groupe de sécurité réseau pour autoriser les connexions internet via le port 80 avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un groupe de sécurité réseau avec [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* :

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Créer des cartes réseau

Créer des cartes réseau virtuelles avec [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). L’exemple suivant crée deux cartes réseau virtuelles à la fois avec des configurations IPv4 et IPv6. (Une carte d’interface réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Vous pouvez maintenant créer les machines virtuelles avec [New-AzVM](/powershell/module/az.compute/new-azvm). L’exemple suivant crée deux machines virtuelles ainsi que les composants de réseau virtuel nécessaires s’ils n’existent pas encore. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Déterminer les adresses IP des points de terminaison IPv4 et IPv6
Obtenir tous les objets d’Interface réseau dans le groupe de ressources permet de synthétiser les IP utilisé dans ce déploiement avec `get-AzNetworkInterface`. En outre, obtenir les adresses de serveur frontal de l’équilibreur de charge des points de terminaison IPv4 et IPv6 avec `get-AzpublicIpAddress`.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
La figure suivante illustre un exemple de sortie qui répertorie les adresses IPv4 et IPv6 privées des deux machines virtuelles et les adresses de serveur frontal IPv4 et IPv6 de l’équilibreur de charge.

![Résumé des IP double pile (IPv4/IPv6) du déploiement d’application dans Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Réseau virtuel de mode IPv6 double pile dans le portail Azure
Vous pouvez afficher le réseau virtuel de double pile IPv6 dans le portail Azure comme suit :
1. Dans la barre de recherche du portail, entrez *dsVnet*.
2. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la. Cette opération lance le **vue d’ensemble** page du réseau virtuel double pile nommé *dsVnet*. Le réseau virtuel de pile double montre les deux cartes réseau avec des configurations à la fois IPv4 et IPv6 situées dans le sous-réseau de pile double nommé *dsSubnet*.

  ![IPv6 double pile réseau virtuel Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> L’adresse IPv6 pour le réseau virtuel Azure est disponible dans le portail Azure en lecture seule pour cette version préliminaire.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un équilibreur de charge avec une configuration IP de frontend double (IPv4 et IPv6). Vous avez également créé un deux machines virtuelles incluant des cartes réseau avec deux configurations IP (IPV4 + IPv6) qui ont été ajoutées pour le pool back-end de l’équilibreur de charge. Pour en savoir plus sur la prise en charge de IPv6 dans les réseaux virtuels Azure, consultez [Nouveautés IPv6 pour le réseau virtuel Azure ?](ipv6-overview.md)