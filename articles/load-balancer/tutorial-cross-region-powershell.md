---
title: 'Tutoriel : Créer un équilibreur de charge inter-régions à l’aide d’Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ce tutoriel explique comment déployer un équilibreur de charge Azure inter-régions à l’aide d’Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721342"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Tutoriel : Créer un équilibreur de charge Azure inter-régions à l’aide du portail Azure

Un équilibreur de charge interrégional garantit la disponibilité mondiale du service dans plusieurs régions Azure. En cas de défaillance d'une région, le trafic est acheminé vers l'équilibreur de charge régional sain le plus proche.  

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un équilibreur de charge interrégional.
> * Créez une règle d’équilibreur de charge.
> * Créer un pool de back-ends contenant deux équilibreurs de charge régionaux.
> * Tester l’équilibreur de charge.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure.
- Deux équilibreurs de charge Azure de SKU **standard** avec des pools de back-ends déployés dans deux régions Azure différentes.
    - Pour plus d’informations sur la création d’un équilibreur de charge standard régional et de machines virtuelles pour les pools de back-ends, consultez [Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge des machines virtuelles à l’aide d’Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Ajoutez **-R1** et **-R2** au nom des équilibreurs de charge et des machines virtuelles dans chaque région. 
- Azure PowerShell installé localement ou Azure Cloud Shell.


Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-cross-region-load-balancer"></a>Créer un équilibreur de charge interrégional


### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Créer des ressources pour l’équilibreur de charge interrégional

Dans cette section, vous allez créer les ressources nécessaires pour l’équilibrage de charge interrégional.

Une adresse IP publique de référence SKU standard globale est utilisée pour le front-end de l’équilibreur de charge interrégional.

* Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer l’adresse IP publique.

* Créez une configuration IP front-end avec [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Créez un pool d’adresses back-end avec [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Créez une règle d’équilibreur de charge avec [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Créez un équilibreur de charge interrégional avec [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Configuration du pool principal

Dans cette section, vous allez ajouter deux équilibreurs de charge standard régionaux au pool de back-ends de l’équilibreur de charge interrégional.

> [!IMPORTANT]
> Pour effectuer ces étapes, vérifiez que deux équilibreurs de charge régionaux avec des pools de back-ends ont été déployés dans votre abonnement.  Pour plus d’informations, consultez **[Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge des machines virtuelles à l’aide d’Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)** .

* Utilisez [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) et [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) pour stocker les informations de l’équilibreur de charge régional dans des variables.

* Utilisez [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) pour créer la configuration du pool d’adresses back-end pour l’équilibreur de charge.

* Utilisez [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) pour ajouter le front-end de l’équilibreur de charge régional au pool de back-ends interrégional.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Dans cette section, vous allez tester l’équilibreur de charge interrégional. Vous allez vous connecter à l’adresse IP publique dans un navigateur web.  Vous allez arrêter les machines virtuelles dans l’un des pools de back-ends de l’équilibreur de charge régional et observer le basculement.

1. Utilisez [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pour obtenir l’adresse IP publique de l’équilibreur de charge :

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

3. Arrêtez les machines virtuelles dans le pool de back-ends de l’un des équilibreurs de charge régionaux.

4. Actualisez le navigateur web et observez le basculement de la connexion vers l’autre équilibreur de charge régional.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n'en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, l'équilibreur de charge et les ressources restantes.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

* créé une adresse IP globale.
* Vous avez créé un équilibreur de charge interrégional.
* Vous avez créé une règle d’équilibrage de charge.
* Vous avez ajouté des équilibreurs de charge régionaux au pool de back-ends de l’équilibreur de charge interrégional.
* Vous avez testé l’équilibreur de charge.


Passez à l’article suivant pour savoir comment…
> [!div class="nextstepaction"]
> [Équilibrer la charge de machines virtuelles entre des zones de disponibilité](tutorial-load-balancer-standard-public-zone-redundant-portal.md)