---
title: Déployer et configurer une stratégie de pare-feu Azure à l’aide d’Azure PowerShell
description: Dans cet article, vous découvrez comment déployer et configurer une stratégie de pare-feu Azure à l’aide d’Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 05/03/2021
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 61ef026e89b89af17a062af29be41256dfbc8d94
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776714"
---
# <a name="deploy-and-configure-azure-firewall-policy-using-azure-powershell"></a>Déployer et configurer une stratégie de pare-feu Azure à l’aide d’Azure PowerShell

Le contrôle de l’accès réseau sortant est une partie importante d’un plan de sécurité réseau global. Par exemple, vous pouvez souhaiter limiter l’accès aux sites web. Vous pouvez aussi vouloir limiter l’accès à certaines adresses IP et à certains ports sortants.

Vous pouvez contrôler l’accès réseau sortant à partir d’un sous-réseau Azure à l’aide du Pare-feu Azure et d’une stratégie de pare-feu. Avec le Pare-feu Azure, vous pouvez configurer les éléments suivants :

* Règles d’application qui définissent des noms de domaine complets (FQDN) accessibles depuis un sous-réseau.
* Règles réseau qui définissent l’adresse source, le protocole, le port de destination et l’adresse de destination.

Le trafic réseau est soumis aux règles de pare-feu configurées lorsque vous routez votre trafic réseau vers le pare-feu en tant que sous-réseau de passerelle par défaut.

Dans le cadre de cet article, vous créez un seul réseau virtuel simplifié avec trois sous-réseaux pour un déploiement facile. Pour les déploiements de production, un [modèle Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), dans lequel le pare-feu est dans son propre réseau virtuel, est recommandé. Les serveurs de la charge de travail se trouvent dans des réseaux virtuels appairés dans la même région avec un ou plusieurs sous-réseaux.

* **AzureFirewallSubnet** : le pare-feu est dans ce sous-réseau.
* **Workload-SN** : le serveur de la charge de travail est dans ce sous-réseau. Le trafic réseau de ce sous-réseau traverse le pare-feu.
* **AzureBastionSubnet** : sous-réseau utilisé pour Azure Bastion, qui est utilisé pour se connecter au serveur de charge de travail. Pour plus d’informations sur Azure Bastion, consultez [Présentation d’Azure Bastion](../bastion/bastion-overview.md).

![Tutoriel relatif à l’infrastructure réseau](media/deploy-ps/tutorial-network.png)

Dans cet article, vous apprendrez comment :


* Configurer un environnement réseau de test
* Déployer un pare-feu
* Créer un itinéraire par défaut
* Créer une stratégie de pare-feu
* Configurer une règle d’application pour autoriser l’accès à www.google.com
* Configurer une règle de réseau pour autoriser l’accès aux serveurs DNS externes
* Tester le pare-feu

Si vous préférez, vous pouvez suivre cette procédure en utilisant le [portail Azure](tutorial-firewall-deploy-portal-policy.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour cette procédure, vous devez exécuter PowerShell localement. Les modules Azure PowerShell doivent être installés. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Après avoir vérifié la version PowerShell, exécutez `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="set-up-the-network"></a>Configurer le réseau

Tout d’abord, créez un groupe de ressources qui contiendra les ressources nécessaires pour déployer le pare-feu. Ensuite, créez un réseau virtuel, des sous-réseaux et des serveurs de test.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Le groupe de ressources contient toutes les ressources nécessaires à ce déploiement.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Créer un réseau virtuel et un hôte Azure Bastion

Ce réseau virtuel comporte trois sous-réseaux :

> [!NOTE]
> La taille du sous-réseau AzureFirewallSubnet est /26. Pour plus d’informations sur la taille du sous-réseau, consultez le [FAQ Pare-feu Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

```azurepowershell
$Bastionsub = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.0.0/27
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
```
Créez maintenant le réseau virtuel :

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $Bastionsub, $FWsub, $Worksub
```
### <a name="create-public-ip-address-for-azure-bastion-host"></a>Créer une adresse IP publique pour l’hôte Azure Bastion

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName Test-FW-RG -Location "East US" `
   -Name Bastion-pip -AllocationMethod static -Sku standard
```

### <a name="create-azure-bastion-host"></a>Créer un hôte Azure Bastion

```azurepowershell
New-AzBastion -ResourceGroupName Test-FW-RG -Name Bastion-01 -PublicIpAddress $publicip -VirtualNetwork $testVnet
```
### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

À présent, créez la machine virtuelle de charge de travail et placez-la dans le sous-réseau approprié.
Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe pour la machine virtuelle.


Créez un machine virtuelle de charge de travail.
Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe pour la machine virtuelle.

```azurepowershell
#Create the NIC
$wsn = Get-AzVirtualNetworkSubnetConfig -Name  Workload-SN -VirtualNetwork $testvnet
$NIC01 = New-AzNetworkInterface -Name Srv-Work -ResourceGroupName Test-FW-RG -Location "East us" -Subnet $wsn

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC01.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2019-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="create-a-firewall-policy"></a>Créer une stratégie de pare-feu

```azurepowershell
$fwpol = New-AzFirewallPolicy -Name fw-pol -ResourceGroupName Test-FW-RG -Location eastus
```
## <a name="configure-a-firewall-policy-application-rule"></a>Configurer une règle d’application de stratégie de pare-feu

La règle d’application autorise un accès sortant vers `www.google.com`.

```azurepowershell
$RCGroup = New-AzFirewallPolicyRuleCollectionGroup -Name AppRCGroup -Priority 100 -FirewallPolicyObject $fwpol
$apprule1 = New-AzFirewallPolicyApplicationRule -Name Allow-google -SourceAddress "10.0.2.0/24" -Protocol "http:80","https:443" -TargetFqdn www.google.com
$appcoll1 = New-AzFirewallPolicyFilterRuleCollection -Name App-coll01 -Priority 100 -Rule $appRule1 -ActionType "Allow"
Set-AzFirewallPolicyRuleCollectionGroup -Name $RCGroup.Name -Priority 100 -RuleCollection $appcoll1 -FirewallPolicyObject $fwPol
```

Le Pare-feu Azure comprend un regroupement de règles intégré pour les noms de domaine complets d’infrastructure qui sont autorisés par défaut. Ces noms de domaine complets sont spécifiques à la plateforme et ne peuvent pas être utilisés à d’autres fins. Pour plus d’informations, consultez [Noms de domaine complets d’infrastructure](infrastructure-fqdns.md).

## <a name="configure-a-firewall-policy-network-rule"></a>Configurer une règle de réseau de stratégie de pare-feu

La règle de réseau autorise un accès sortant vers deux adresses IP sur le port 53 (DNS).

```azurepowershell
$RCGroup = New-AzFirewallPolicyRuleCollectionGroup -Name NetRCGroup -Priority 200 -FirewallPolicyObject $fwpol
$netrule1 = New-AzFirewallPolicyNetworkRule -name Allow-DNS -protocol UDP -sourceaddress 10.0.2.0/24 -destinationaddress 209.244.0.3,209.244.0.4 -destinationport 53
$netcoll1 = New-AzFirewallPolicyFilterRuleCollection -Name Net-coll01 -Priority 200 -Rule $netrule1 -ActionType "Allow"
Set-AzFirewallPolicyRuleCollectionGroup -Name $RCGroup.Name -Priority 200 -RuleCollection $netcoll1 -FirewallPolicyObject $fwPol
```

## <a name="deploy-the-firewall"></a>Déployer le pare-feu

Déployez maintenant le pare-feu dans le réseau virtuel.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetwork $testVnet -PublicIpAddress $FWpip -FirewallPolicyId $fwpol.Id


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



## <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modifier les adresses DNS principales et secondaires de l’interface réseau **Srv-Work**

À des fins de test dans cette procédure, configurez les adresses DNS principales et secondaires du serveur. Ceci n’est pas obligatoire pour le pare-feu Azure.

```azurepowershell
$NIC01.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC01.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC01 | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Tester le pare-feu

Testez maintenant le pare-feu pour vérifier qu’il fonctionne comme prévu.

1. Connectez-vous à une machine virtuelle **Srv-Work** à l’aide de Bastion, puis ouvrez une session. 

   :::image type="content" source="media/deploy-ps/bastion.png" alt-text="Connectez-vous à l’aide de Bastion.":::

3. Sur **Srv-Work**, ouvrez une fenêtre PowerShell et exécutez les commandes suivantes :

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

Maintenant que vous avez vérifié que les règles de stratégie de pare-feu fonctionnent :

* Vous pouvez résoudre les noms DNS à l’aide du serveur DNS externe configuré.
* Vous pouvez accéder au nom de domaine complet autorisé, mais pas à d’autres.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez garder vos ressources de pare-feu à des fins de test complémentaire ou, si vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **Test-FW-RG** pour supprimer toutes les ressources associées au pare-feu :

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Superviser les journaux d’activité de Pare-feu Azure](./firewall-diagnostics.md)
