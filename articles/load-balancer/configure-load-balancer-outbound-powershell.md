---
title: Configurer des règles d'équilibrage de charge et des règles de trafic sortant à l'aide d'Azure PowerShell
titlesuffix: Azure Load Balancer
description: Cet article explique comment configurer des règles d'équilibrage de charge et des règles de trafic sortant dans une instance de Standard Load Balancer à l'aide d'Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263056"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Configurer des règles d'équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l'aide d'Azure PowerShell

Cet article explique comment configurer des règles de trafic sortant dans Standard Load Balancer à l'aide d'Azure PowerShell.  

Lorsque vous aurez terminé, la ressource d'équilibrage de charge contiendra deux frontends et les règles associées : une règle pour le trafic entrant et une règle pour le trafic sortant.  Chaque frontend référence une adresse IP publique. Ce scénario utilise une adresse IP publique différente pour le trafic entrant et le trafic sortant.   La règle d’équilibrage de charge gère uniquement l’équilibrage de charge du trafic entrant. La règle de trafic sortant contrôle la NAT de trafic sortant sur la machine virtuelle.  Cet article utilise deux pools backend distincts, un pour le trafic entrant et l'autre pour le trafic sortant, afin d'illustrer la fonctionnalité et de permettre une certaine flexibilité pour ce scénario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Se connecter à un compte Azure AD
Connectez-vous à votre abonnement Azure avec la commande [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) et suivez les instructions qui s'affichent à l’écran :
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myresourcegroupoutbound* à l’emplacement *eastus2* :

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Création d’un réseau virtuel
Créez un réseau virtuel nommé *myvnetoutbound* avec un sous-réseau nommé *mysubnetoutbound* dans *myresourcegroupoutbound* à l'aide des commandes [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) et [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0) :

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Créer une adresse IP publique entrante 

Pour accéder à votre application web sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. Standard Load Balancer prend uniquement en charge les adresses IP publiques standard. Utilisez la commande [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) pour créer une adresse IP publique standard nommée *mypublicipinbound* dans *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Créer une adresse IP publique sortante 

À l'aide de la commande [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0), créez une adresse IP standard pour la configuration sortante frontend de l'équilibreur de charge.

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Créer un équilibreur de charge Azure

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :
  - Une IP frontend qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  - Un pool backend où l’IP frontend envoie le trafic réseau à charge équilibrée.
  - Un pool backend pour la connexion sortante. 
  - Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  - Une règle de trafic entrant utilisée par l’équilibreur de charge pour distribuer le trafic à destination des machines virtuelles.
  - Une règle de trafic sortant utilisée par l’équilibreur de charge pour distribuer le trafic en provenance des machines virtuelles.

### <a name="create-inbound-frontend-ip"></a>Créer une configuration IP frontend entrante
À l'aide de la commande [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0), créez la configuration IP frontend sortante de l'équilibreur de charge, qui inclut une configuration IP frontend entrante nommée *myfrontendinbound* et associée à l'adresse IP publique *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Créer une IP frontend sortante
À l'aide de la commande [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0), créez la configuration IP frontend sortante de l'équilibreur de charge, qui inclut une configuration IP frontend sortante nommée *myfrontendoutbound* et associée à l'adresse IP publique *mypublicipoutbound* :

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Créer un pool backend entrant
À l'aide de la commande [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0), créez le pool backend entrant de l'équilibreur de charge, *bepoolinbound* :

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Créer un pool backend sortant
À l'aide de la commande [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0), créez un pool d'adresses backend supplémentaires, *bepooloutbound*, pour définir la connectivité sortante d'un pool de machines virtuelles. La création d’un pool sortant distinct offre une flexibilité maximale. Toutefois, vous pouvez omettre cette étape et utiliser uniquement le trafic entrant *bepoolinbound* également.  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Créer une sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre le trafic réseau. L’instance de machine virtuelle présentant des contrôles de sonde défaillants est supprimée de l’équilibrage de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité. À l'aide de la commande [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0), créez une sonde d'intégrité pour surveiller l'intégrité des machines virtuelles. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Créer une règle d’équilibrage de charge

Une règle d’équilibreur de charge définit la configuration IP frontend pour le trafic entrant et le pool backend pour recevoir le trafic, ainsi que le port source et le port de destination requis. À l'aide de la commande [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0), créez une règle d'équilibrage de charge nommée *myinboundlbrule* pour écouter le port 80 dans le pool frontend *myfrontendinbound* et envoyer le trafic réseau à charge équilibrée vers le pool d'adresses backend *bepoolinbound* également sur le port 80. 

>[!NOTE]
>Cette règle d'équilibrage de charge désactive la (S)NAT de trafic sortant automatique, du fait de la définition du paramètre **-DisableOutboundSNAT**. La NAT de trafic sortant est uniquement fournie par la règle de trafic sortant.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Créer une règle de trafic sortant

Une règle de trafic sortant définit l’adresse IP publique frontend, représentée par la configuration frontend *myfrontendoutbound*, utilisée pour l’ensemble du trafic NAT sortant, ainsi que le pool backend auquel cette règle s’applique.  Créez une règle de trafic sortant *myoutboundrule* pour la NAT de trafic sortant sur toutes les machines virtuelles (configurations IP de carte réseau) du pool backend *bepool*.  La commande ci-dessous change également le délai d’inactivité sortant de 4 à 15 minutes, et alloue 10 000 ports SNAT au lieu de 1 024.  Pour plus d'informations, consultez [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Si vous ne souhaitez pas utiliser de pool sortant distinct, vous pouvez modifier l'argument du pool d'adresses dans la commande précédente pour spécifier *$bepoolin* à la place.  Nous vous recommandons d'utiliser des pools distincts pour plus de flexibilité et de lisibilité de la configuration qui en résulte.

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

À l'aide de la commande [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0), créez un équilibreur de charge *lb* avec l'adresse IP entrante, qui inclut une configuration d'adresse IP frontend entrante et un pool backend *bepoolinbound* qui est associé à l'adresse IP publique *mypublicipinbound* créée à l'étape précédente.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Vous pouvez alors ajouter vos machines virtuelles au pool backend *bepoolinbound* __et__  *bepooloutbound* en mettant à jour la configuration IP des ressources de carte réseau respectives à l'aide de la commande [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n'en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) pour supprimer le groupe de ressources, l'équilibreur de charge et toutes les ressources associées.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé une instance de Standard Load Balancer, configuré des règles d'équilibrage de charge et des règles de trafic sortant, et configuré une sonde d'intégrité pour les machines virtuelles dans le pool backend. Pour en savoir plus sur Azure Load Balancer, consultez les didacticiels qui lui sont consacrés.

> [!div class="nextstepaction"]
> [Didacticiels Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
