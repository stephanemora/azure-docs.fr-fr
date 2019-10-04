---
title: 'Démarrage rapide : Créer un équilibrage de charge de base - Azure PowerShell'
titlesuffix: Azure Load Balancer
description: Ce démarrage rapide montre comment créer un équilibreur de charge de base à l’aide de PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 378904b139edb7fe5d7c4376102ca6b153d84fb6
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129074"
---
# <a name="get-started"></a>Démarrage rapide : Créer un équilibreur de charge public à l’aide d’Azure PowerShell

Ce démarrage rapide vous montre comment créer un équilibreur de charge de base à l’aide d’Azure PowerShell. Pour tester l’équilibreur de charge, vous déployez deux machines virtuelles exécutant un serveur Windows, puis vous équilibrez la charge d’une application web entre les machines virtuelles.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de créer votre équilibreur de charge, vous devez créer un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupLB* à l’emplacement *EastUS* :

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique
Pour accéder à votre application sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. Créez une adresse IP publique avec [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). L’exemple suivant crée une adresse IP publique nommée *myPublicIP* dans le groupe de ressources *myResourceGroupLB* :

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

## <a name="create-basic-load-balancer"></a>Créer un équilibreur de charge de base

Dans cette section, vous allez établir la configuration IP front-end et configurer le pool d’adresses back-end pour l’équilibreur de charge, puis créer l’équilibreur de charge Basic Load Balancer.

### <a name="create-front-end-ip"></a>Créer une configuration IP front-end

Créez une configuration IP front-end avec [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). L’exemple suivant crée une configuration IP de serveur frontal nommée *myFrontEnd* et joint l’adresse *myPublicIP* :

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>Configurer un pool d’adresses back-end

Créez un pool d’adresses back-end avec [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Les machines virtuelles se joignent à ce pool back-end dans les étapes suivantes. L’exemple suivant crée un pool d’adresses back-end nommé *myBackEndPool* :

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité
Pour permettre à l’équilibrage de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Par défaut, une machine virtuelle est supprimée de la distribution d’équilibrage de charge après deux échecs consécutifs à des intervalles de 15 secondes. Vous créez une sonde d’intégrité selon un protocole ou une page de vérification d’intégrité spécifique pour votre application. 

L’exemple suivant permet de créer une sonde TCP. Vous pouvez également créer des sondes HTTP personnalisées pour des contrôles d’intégrité plus affinés. Lorsque vous utilisez une sonde HTTP personnalisée, vous devez créer la page de contrôle d’intégrité, par exemple *healthcheck.aspx*. La sonde doit retourner une réponse **HTTP 200 OK** pour l’équilibreur de charge pour assurer la rotation de l’hôte.

Pour créer une sonde d’intégrité TCP, utilisez [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). L’exemple suivant crée une sonde d’intégrité nommée *myHealthProbe* qui surveille chaque machine virtuelle sur le port *HTTP* *80* :

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Pour veiller à ce que seules les machines virtuelles saines reçoivent le trafic, vous devez également définir la sonde d’intégrité à utiliser.

Créez une règle d’équilibreur de charge avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). L’exemple suivant crée une règle d’équilibreur de charge nommée *myLoadBalancerRule* et équilibre le trafic sur le port *TCP* *80* :

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Créer les règles NAT

Créez des règles NAT avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). L’exemple suivant crée des règles NAT nommées *myLoadBalancerRDP1* et *myLoadBalancerRDP2* pour autoriser des connexions RDP aux serveurs principaux avec les ports 4221 et 4222 :

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

Créez l’équilibreur de charge Basic Load Balancer avec [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). L’exemple suivant crée un équilibreur de charge de base public nommé myLoadBalancer à l’aide de la configuration IP de serveur frontal, du pool principal, de la sonde d’intégrité, de la règle d’équilibrage de charge et des règles NAT que vous avez créés dans les étapes précédentes :

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur, vous devez créer des ressources de réseau virtuel de prise en charge (réseau virtuel et cartes réseau virtuelles). 

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* :

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau

Créez un groupe de sécurité réseau pour définir les connexions entrantes vers votre réseau virtuel.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Créer une règle de groupe de sécurité réseau pour le port 3389

Créez une règle de groupe de sécurité réseau pour autoriser les connexions RDP via le port 3389 avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Créer une règle de groupe de sécurité réseau pour le port 80

Créez une règle de groupe de sécurité réseau pour autoriser les connexions entrantes via le port 80 avec [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un groupe de sécurité réseau avec [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Créer des cartes réseau

Créez des cartes réseau virtuelles avec [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). L’exemple suivant crée deux cartes réseau virtuelles. (Une carte d’interface réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes). Vous pouvez créer des machines virtuelles et des cartes d’interface réseau virtuelles supplémentaires à tout moment et les ajouter à l’équilibreur de charge :

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Pour améliorer la haute disponibilité de votre application, placez vos machines virtuelles dans un groupe à haute disponibilité.

Créez un groupe à haute disponibilité avec la commande [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). L’exemple suivant permet de créer un groupe à haute disponibilité nommé *myAvailabilitySet* :

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Vous pouvez maintenant créer les machines virtuelles avec [New-AzVM](/powershell/module/az.compute/new-azvm). L’exemple suivant crée deux machines virtuelles ainsi que les composants de réseau virtuel nécessaires s’ils n’existent pas encore. Dans cet exemple, les cartes réseau (*VM1* et *VM2*) créées à l’étape précédente sont automatiquement affectées aux machines virtuelles *VM1* et *VM2*, car leur nom est identique et le réseau virtuel (*myVnet*) et le sous-réseau (*mySubnet*) qui leur sont affectés sont les mêmes. Par ailleurs, comme les cartes réseau sont associées au pool back-end de l’équilibreur de charge, les machines virtuelles sont automatiquement ajoutées au pool back-end.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Le paramètre `-AsJob` crée la machine virtuelle en tant que tâche en arrière-plan. Vous recevez donc les invites PowerShell. Vous pouvez afficher les détails des travaux en arrière-plan à l’aide du cmdlet `Job`. La création et la configuration des deux machines virtuelles prennent quelques minutes.

### <a name="install-iis-with-custom-web-page"></a>Installer IIS avec une page web personnalisée
 
Installez IIS avec une page web personnalisée sur les deux machines virtuelles principales comme suit :

1. Obtenez l’adresse IP publique de l’équilibreur de charge. À l’aide de `Get-AzPublicIPAddress`, obtenez l’adresse IP publique de l’équilibreur de charge.

    ```azurepowershell-interactive
    Get-AzPublicIPAddress -ResourceGroupName "myResourceGroupLB" -Name "myPublicIP" | select IpAddress
    ```

2. **Sur votre ordinateur local, ouvrez une invite de commandes ou une fenêtre PowerShell pour cette étape**.  Créez une connexion Bureau à distance à VM1 à l’aide de l’adresse IP publique que vous avez obtenue dans l’étape précédente. 

    ```azurepowershell-interactive
    mstsc /v:PublicIpAddress:4221  
    ```

3. Entrez les informations d’identification de *VM1* pour démarrer la session RDP.
4. Lancez Windows PowerShell sur VM1 et utilisez les commandes suivantes pour installer le serveur IIS et mettre à jour le fichier htm par défaut.

    ```azurepowershell-interactive
        # Install IIS
          Install-WindowsFeature -name Web-Server -IncludeManagementTools
        
        # Remove default htm file
          remove-item  C:\inetpub\wwwroot\iisstart.htm
        
        # Add custom htm file
          Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Fermez la connexion RDP à *myVM1*.
6. **Créez une connexion RDP sur votre ordinateur local** à *myVM2* en exécutant la commande `mstsc /v:PublicIpAddress:4222`, puis répétez l’étape 4 pour *VM2*.

## <a name="test-load-balancer"></a>Tester l’équilibreur de charge
Obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). L’exemple suivant obtient l’adresse IP pour *myPublicIP* créée précédemment :

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName "myResourceGroupLB" -Name "myPublicIP" | select IpAddress
```

Vous pouvez alors entrer l’adresse IP publique dans un navigateur web. Le site web s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibreur de charge a distribué le trafic comme dans l’exemple suivant :

![Tester l’équilibreur de charge](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Pour visualiser la distribution de trafic par l’équilibreur de charge sur les deux machines virtuelles exécutant votre application, vous pouvez forcer l’actualisation de votre navigateur web.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un équilibreur de charge de base, associé des machines virtuelles à celui-ci, configuré la règle de trafic d’équilibreur de charge, la sonde d’intégrité, puis testé l’équilibreur de charge. Pour en savoir plus sur Azure Load Balancer, consultez les didacticiels qui lui sont consacrés.

> [!div class="nextstepaction"]
> [Didacticiels Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)