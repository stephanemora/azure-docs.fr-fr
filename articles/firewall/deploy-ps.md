---
title: Déployer et configurer un pare-feu Azure à l’aide de Azure PowerShell
description: Dans cet article, vous découvrez comment déployer et configurer un pare-feu Azure avec Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/28/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c720d7c261421ade9dfce01f0b116123dcab1e55
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071701"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Déployer et configurer un pare-feu Azure à l’aide de Azure PowerShell

Le contrôle de l’accès réseau sortant est une partie importante d’un plan de sécurité réseau global. Par exemple, vous pouvez souhaiter limiter l’accès aux sites web. Vous pouvez aussi vouloir limiter l’accès à certaines adresses IP et à certains ports sortants.

Vous pouvez contrôler l’accès réseau sortant à partir d’un sous-réseau Azure à l’aide du Pare-feu Azure. Avec le Pare-feu Azure, vous pouvez configurer les éléments suivants :

* Règles d’application qui définissent des noms de domaine complets (FQDN) accessibles depuis un sous-réseau.
* Règles réseau qui définissent l’adresse source, le protocole, le port de destination et l’adresse de destination.

Le trafic réseau est soumis aux règles de pare-feu configurées lorsque vous routez votre trafic réseau vers le pare-feu en tant que sous-réseau de passerelle par défaut.

Dans le cadre de cet article, vous créez un seul réseau virtuel simplifié avec trois sous-réseaux pour un déploiement facile. Pour les déploiements de production, un [modèle Hub and Spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), dans lequel le pare-feu est dans son propre réseau virtuel, est recommandé. Les serveurs de la charge de travail se trouvent dans des réseaux virtuels appairés dans la même région avec un ou plusieurs sous-réseaux.

* **AzureFirewallSubnet** : le pare-feu est dans ce sous-réseau.
* **Workload-SN** : le serveur de la charge de travail est dans ce sous-réseau. Le trafic réseau de ce sous-réseau traverse le pare-feu.
* **Jump-SN** : le serveur « jump » est dans ce sous-réseau. Le serveur de rebond possède une adresse IP publique à laquelle vous pouvez vous connecter à l’aide du Bureau à distance. De là, vous pouvez alors vous connecter (à l’aide d’un autre Bureau à distance) au serveur de la charge de travail.

![Tutoriel relatif à l’infrastructure réseau](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Dans cet article, vous apprendrez comment :


* Configurer un environnement réseau de test
* Déployer un pare-feu
* Créer un itinéraire par défaut
* Configurer une règle d’application pour autoriser l’accès à www.google.com
* Configurer une règle de réseau pour autoriser l’accès aux serveurs DNS externes
* Tester le pare-feu

Si vous préférez, vous pouvez suivre cette procédure en utilisant le [portail Azure](tutorial-firewall-deploy-portal.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour cette procédure, vous devez exécuter PowerShell localement. Les modules Azure PowerShell doivent être installés. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Après avoir vérifié la version PowerShell, exécutez `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="set-up-the-network"></a>Configurer le réseau

Tout d’abord, créez un groupe de ressources qui contiendra les ressources nécessaires pour déployer le pare-feu. Ensuite, créez un réseau virtuel, des sous-réseaux et des serveurs de test.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Le groupe de ressources contient toutes les ressources nécessaires à ce déploiement.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Créer un réseau virtuel

Ce réseau virtuel comporte trois sous-réseaux :

> [!NOTE]
> La taille du sous-réseau AzureFirewallSubnet est /26. Pour plus d’informations sur la taille du sous-réseau, consultez le [FAQ Pare-feu Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
Créez maintenant le réseau virtuel :

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Maintenant créez les machines virtuelles de rebond et de charge de travail, et placez-les dans les sous-réseaux appropriés.
Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe pour la machine virtuelle.

Créez la machine virtuelle Srv-Jump.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Créez une machine virtuelle de charge de travail sans adresse IP publique.
Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe pour la machine virtuelle.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Déployer le pare-feu

Déployez maintenant le pare-feu dans le réseau virtuel.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Notez l’adresse IP privée. Vous l’utiliserez plus tard lors de la création de l’itinéraire par défaut.

## <a name="create-a-default-route"></a>Créer un itinéraire par défaut

Créer une table, avec la propagation de routes BGP désactivée

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Configurer une règle d’application

La règle d’application autorise un accès sortant vers www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

Le Pare-feu Azure comprend un regroupement de règles intégré pour les noms de domaine complets d’infrastructure qui sont autorisés par défaut. Ces noms de domaine complets sont spécifiques à la plateforme et ne peuvent pas être utilisés à d’autres fins. Pour plus d’informations, consultez [Noms de domaine complets d’infrastructure](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurer une règle de réseau

La règle de réseau autorise un accès sortant vers deux adresses IP sur le port 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modifier les adresses DNS principales et secondaires de l’interface réseau **Srv-Work**

À des fins de test dans cette procédure, configurez les adresses DNS principales et secondaires du serveur. Ceci n’est pas obligatoire pour le pare-feu Azure.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Tester le pare-feu

Testez maintenant le pare-feu pour vérifier qu’il fonctionne comme prévu.

1. Notez l’adresse IP privée de la machine virtuelle **Srv-Work** :

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Connectez un bureau à distance à la machine virtuelle **Srv-Jump**, puis connectez-vous. De là, ouvrez une connexion Bureau à distance à l’adresse IP privée **Srv-Work** et connectez-vous.

3. Sur **SRV-Work**, ouvrez une fenêtre PowerShell et exécutez les commandes suivantes :

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Les deux commandes doivent retourner des réponses, montrant que vos requêtes DNS passent au travers du pare-feu.

1. Exécutez les commandes suivantes :

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Les requêtes `www.google.com` doivent réussir, et les requêtes `www.microsoft.com` échouer. Cet exemple montre que vos règles de pare-feu fonctionnent comme prévu.

Maintenant que vous avez vérifié que les règles de pare-feu fonctionnent :

* Vous pouvez résoudre les noms DNS à l’aide du serveur DNS externe configuré.
* Vous pouvez accéder au nom de domaine complet autorisé, mais pas à d’autres.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Conservez vos ressources de pare-feu pour le prochain tutoriel ou, si vous n’en avez plus besoin, supprimez le groupe de ressources **Test-FW-RG** pour supprimer toutes les ressources associées au pare-feu :

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Surveiller les journaux d’activité de Pare-feu Azure](./tutorial-diagnostics.md)
