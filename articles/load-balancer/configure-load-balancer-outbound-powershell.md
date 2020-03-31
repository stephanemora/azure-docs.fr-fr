---
title: Configurer des règles d’équilibrage de charge et des règles de trafic sortant à l’aide d’Azure PowerShell
titleSuffix: Azure Load Balancer
description: Cet article explique comment configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225438"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure PowerShell

Cet article explique comment configurer des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure PowerShell.  

Une fois le scénario de cet article terminé, la ressource d’équilibreur de charge contient deux front-ends et les règles qui leur sont associées. Vous disposez d’un front-end pour le trafic entrant et d’un autre front-end pour le trafic sortant.  

Chaque front-end fait référence à une adresse IP publique. Dans ce scénario, l’adresse IP publique pour le trafic entrant est différente de l’adresse pour le trafic sortant.   La règle d’équilibrage de charge fournit uniquement l’équilibrage de charge du trafic entrant. La règle de trafic sortant contrôle la traduction d’adresses réseau (NAT) sortante pour la machine virtuelle.  

Le scénario utilise deux pools back-end : un pour le trafic entrant et un pour le trafic sortant. Ces pools illustrent la fonctionnalité et offrent une certaine flexibilité pour le scénario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure
Connectez-vous à votre abonnement Azure à l’aide de la commande [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Ensuite, suivez les instructions à l’écran.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources à l’aide de [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées. Les ressources sont ensuite gérées à partir du groupe.

L’exemple suivant crée un groupe de ressources nommé *myresourcegroupoutbound* à l’emplacement *eastus2* :

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez un réseau virtuel nommé *myvnetoutbound*. Nommez son sous-réseau *mysubnetoutbound*. Placez-le dans *myresourcegroupoutbound* en utilisant [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) et [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Créer une adresse IP publique entrante 

Pour accéder à votre application web sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. Standard Load Balancer prend uniquement en charge les adresses IP publiques standard. 

Utilisez [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) pour créer une adresse IP publique standard nommée *mypublicipinbound* dans *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Créer une adresse IP publique sortante 

Créez une adresse IP standard pour la configuration du trafic sortant front-end de l’équilibreur de charge en utilisant [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Crée un équilibrage de charge Azure

Cette section explique comment créer et configurer les composants suivants de l’équilibreur de charge :
  - Une adresse IP front-end qui reçoit le trafic réseau entrant sur l’équilibreur de charge
  - Le pool back-end où l’adresse IP front-end envoie le trafic réseau à charge équilibrée
  - Un pool back-end pour la connexion sortante
  - Une sonde d’intégrité qui détermine l’intégrité des instances de machines virtuelles back-end
  - Une règle de trafic entrant d’équilibreur de charge qui définit la façon dont le trafic à destination des machines virtuelles est distribué
  - Une règle de trafic sortant d’équilibreur de charge qui définit la façon dont le trafic en provenance des machines virtuelles est distribué

### <a name="create-an-inbound-front-end-ip"></a>Créer une adresse IP front-end entrante
Créez une configuration d’adresse IP front-end entrante pour l’équilibreur de charge en utilisant [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). L’équilibreur de charge doit inclure une configuration d’adresse IP front-end entrante nommée *myfrontendinbound*. Associez cette configuration à l’adresse IP publique *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Créer une adresse IP front-end sortante
Créez une configuration d’adresse IP front-end sortante pour l’équilibreur de charge en utilisant [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Cet équilibreur de charge doit inclure une configuration IP front-end sortante nommée *myfrontendoutbound*. Associez cette configuration à l’adresse IP publique *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Créer un pool back-end de trafic entrant
Créez le pool back-end de trafic entrant pour l’équilibreur de charge en utilisant [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nommez le pool *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Créer un pool back-end de trafic sortant
Utilisez la commande suivante pour créer un autre pool d’adresses back-end afin de définir la connectivité sortante pour un pool de machines virtuelles en utilisant [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nommez ce pool *bepooloutbound*. 

En créant un pool sortant distinct, vous fournissez une flexibilité maximale. Vous pouvez toutefois omettre cette étape et utiliser uniquement le pool *bepoolinbound* entrant si vous préférez.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machines virtuelles pour garantir qu’elles peuvent envoyer le trafic réseau. L’instance de machine virtuelle qui fait échouer les contrôles de sonde est supprimée de l’équilibreur de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité. 

Pour superviser l’intégrité des machines virtuelles, créez une sonde d’intégrité à l’aide de [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibreur de charge définit la configuration IP front-end pour le trafic entrant et le pool back-end pour recevoir le trafic. Elle définit également le port source et le port de destination exigés. 

Créez une règle d’équilibreur de charge nommée *myinboundlbrule* en utilisant la commande [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Cette règle écoute sur le port 80 dans le pool front-end *myfrontendinbound*. Elle utilise également le port 80 pour envoyer le trafic réseau à charge équilibrée au pool d’adresses back-end *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Cette règle d’équilibrage de charge désactive la traduction d’adresses réseau sécurisée (SNAT) de trafic sortant automatique, en raison du paramètre **-DisableOutboundSNAT**. La NAT de trafic sortant est fournie uniquement par la règle de trafic sortant.

### <a name="create-an-outbound-rule"></a>Créer une règle de trafic sortant

Une règle de trafic sortant définit l’adresse IP publique front-end, qui est représentée par le front-end *myfrontendoutbound*. Ce front-end sera utilisé pour tout le trafic NAT sortant, ainsi que pour le pool back-end auquel la règle s’applique.  

Utilisez la commande suivante pour créer une règle de trafic sortant *myoutboundrule* pour la traduction de réseau sortant sur toutes les machines virtuelles (dans les configurations d’adresse IP publique de carte réseau) du pool back-end *bepool*.  La commande fait passer le délai d’inactivité du trafic sortant de 4 à 15 minutes. Elle alloue 10 000 ports SNAT au lieu de 1 024. Pour plus d’informations, consultez [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Si vous ne souhaitez pas utiliser de pool sortant distinct, vous pouvez modifier l'argument du pool d'adresses dans la commande précédente pour spécifier *$bepoolin* à la place.  Nous vous recommandons d’utiliser des pools distincts pour rendre la configuration qui en résulte flexible et accessible en lecture.

### <a name="create-a-load-balancer"></a>Créer un équilibrage de charge

Utilisez la commande suivante pour créer un équilibreur de charge pour l’adresse IP entrante en utilisant [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Nommez l’équilibreur de charge *lb*. Il doit inclure une configuration d’adresse IP front-end entrante. Son pool back-end *bepoolinbound* doit être associé à l’adresse IP publique*mypublicipinbound* que vous avez créée à l’étape précédente.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

À ce stade, vous pouvez continuer à ajouter vos machines virtuelles aux deux pools back-end *bepoolinbound* et *bepooloutbound* en mettant à jour la configuration IP des ressources de carte réseau respectives. Mettez à jour la configuration des ressources à l’aide de [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, de l’équilibreur de charge et des ressources associées, vous pouvez les supprimer en utilisant [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un équilibreur de charge standard, configuré des règles de trafic d’équilibreur de charge entrant et sortant, et configuré une sonde d’intégrité pour les machines virtuelles dans le pool back-end. 

Pour en savoir plus, passez aux [tutoriels concernant Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
