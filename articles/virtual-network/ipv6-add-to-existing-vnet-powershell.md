---
title: Mettre à niveau une application IPv4 vers IPv6 dans un réseau virtuel Azure – PowerShell
titlesuffix: Azure Virtual Network
description: Cet article montre comment déployer des adresses IPv6 dans une application figurant dans un réseau virtuel Azure avec Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: d08ce1c382d173ac98a0e61e6117ed50b958ba44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119820"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Mettre à niveau une application IPv4 vers IPv6 dans un réseau virtuel Azure – PowerShell (préversion)

Cet article explique comment ajouter une connectivité IPv6 à une application IPv4 existante dans un réseau virtuel Azure avec un Standard Load Balancer et une adresse IP publique. La mise à niveau sur place comprend les éléments suivants :
- Espace d’adressage IPv6 pour le réseau virtuel et le sous-réseau
- Standard Load Balancer avec des configurations frontales IPv4 et IPV6
- Machines virtuelles avec cartes réseau disposant d’une configuration IPv4 + IPv6
- Adresse IP publique IPv6 pour que l'équilibreur de charge dispose d'une connectivité IPv6 accessible sur Internet

> [!Important]
> La prise en charge du protocole IPv6 par le réseau virtuel Azure est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 6.9.0 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="prerequisites"></a>Prérequis

### <a name="register-the-service"></a>Enregistrer le service

Avant de déployer une application double pile dans Azure, vous devez configurer votre abonnement pour cette fonctionnalité d’évaluation à l’aide de la commande Azure PowerShell suivante :

Effectuez l’inscription comme suit :
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Vous pouvez vérifier le statut de l’inscription en exécutant la commande Azure PowerShell suivante : Vérifiez l’inscription en procédant comme suit :
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
À l’issue de l’installation, exécutez la commande suivante :

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Créer un équilibreur de charge standard
Cet article suppose que vous avez déployé un équilibreur de charge standard comme cela est décrit dans [Démarrage rapide : Créer un équilibreur de charge standard - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Récupérer le groupe de ressources

Avant de pouvoir créer un réseau virtuel double pile, vous devez récupérer le groupe de ressources avec [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Créer une adresse IP IPv6

Créez une adresse IPv6 publique avec [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour votre Standard Load Balancer. L’exemple suivant crée une adresse IP publique IPv6 nommée *PublicIP_v6* dans le groupe de ressources *myResourceGroupSLB* :

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Configurer le front-end de l’équilibreur de charge

Récupérez la configuration d’équilibreur de charge existante, puis ajoutez la nouvelle adresse IP IPv6 en utilisant [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) comme suit :

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Configurer le pool back-end de l’équilibreur de charge

Créez le pool back-end sur la copie locale de la configuration d’équilibreur de charge et mettez à jour l’équilibreur de charge en cours d’exécution avec la nouvelle configuration de pool back-end, comme suit :

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Configurer les règles d’équilibreur de charge
Récupérez la configuration existante des pools back-end et front-end d’équilibreur de charge, puis ajoutez de nouvelles règles d’équilibrage de charge avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Ajouter des plages d’adresses IPv6

Ajoutez des plages d’adresses IPv6 au réseau virtuel et au sous-réseau hébergeant les machines virtuelles comme suit :

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Ajouter la configuration IPv6 aux cartes réseau

Configurez toutes les cartes réseau des machines virtuelles avec une adresse IPv6 à l’aide de [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) comme suit :

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Afficher le réseau virtuel double pile IPv6 dans le Portail Microsoft Azure
Vous pouvez afficher le réseau virtuel double pile IPv6 dans le Portail Microsoft Azure en procédant comme suit :
1. Dans la barre de recherche du portail, entrez *myVnet*.
2. Quand le nom **myVnet** apparaît dans les résultats de la recherche, sélectionnez-le. Cela permet d’afficher la page **Vue d’ensemble** du réseau virtuel double pile nommé *myVNet*. Le réseau virtuel double pile montre les trois cartes réseau, avec des configurations IPv4 et IPv6 situées dans le sous-réseau double pile nommé *mySubnet*.

  ![Réseau virtuel double pile IPv6 dans Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> L’adresse IPv6 du réseau virtuel Azure est disponible dans le Portail Microsoft Azure en lecture seule pour cette préversion.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez mis à jour un équilibreur de charge standard existant avec une configuration d’adresse IP front-end IPv4 en une configuration double pile (IPv4 et IPv6). Vous avez également ajouté des configurations IPv6 aux cartes réseau des machines virtuelles du pool principal et au réseau virtuel qui les héberge. Pour en savoir plus sur la prise en charge du protocole IPv6 dans les réseaux virtuels Azure, voir [What is IPv6 for Azure Virtual Network?](ipv6-overview.md) (Rôle d’iPv6 pour un réseau virtuel Azure).
