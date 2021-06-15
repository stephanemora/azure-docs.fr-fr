---
title: Configurer un groupe de machines virtuelles identiques avec un service Azure Load Balancer existant - Azure PowerShell
description: Découvrez comment configurer un groupe de machines virtuelles identiques avec un service Azure Load Balancer existant.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 210bf1c01b31acca1cd68e518a5c765fcd5cc50a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110695741"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Configurer un groupe de machines virtuelles identiques avec un service Azure Load Balancer existant à l’aide d’Azure PowerShell

Dans cet article, vous allez découvrir comment configurer un groupe de machines virtuelles identiques avec un service Azure Load Balancer existant.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure.
- Un équilibreur de charge SKU standard existant dans l’abonnement dans lequel le groupe de machines virtuelles identiques sera déployé.
- Un réseau virtuel Azure pour le groupe de machines virtuelles identiques.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Connectez-vous à Azure CLI

Connectez-vous à Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Déployer un groupe de machines virtuelles identiques avec un équilibreur de charge existant

Remplacez les valeurs entre crochets par les noms des ressources de votre configuration.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

L’exemple ci-dessous déploie un groupe de machines virtuelles identiques avec :

- Nom du groupe de machines virtuelles identiques nommé **myVMSS**
- Azure Load Balancer nommé **myLoadBalancer**
- Nom du pool de back-ends de l’équilibreur de charge nommé **myBackendPool**
- Réseau virtuel Azure nommé **myVnet**
- Sous-réseau nommé **mySubnet**
- Groupe de ressources nommé **myResourceGroup**

```azurepowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Une fois le groupe identique créé, le port principal ne peut pas être modifié lorsqu'une règle d'équilibrage de charge est utilisée par une sonde d'intégrité pour l'équilibreur de charge. Pour modifier le port, vous pouvez supprimer la sonde d'intégrité en mettant à jour le groupe identique de machines virtuelles Azure, puis mettre à jour le port et reconfigurer la sonde d'intégrité.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez déployé un groupe de machines virtuelles identiques avec un service Azure Load Balancer existant.  Pour en savoir plus sur les groupes de machines virtuelles identiques et l’équilibreur de charge, consultez :

- [Qu’est-ce que Azure Load Balancer ?](load-balancer-overview.md)
- [Que sont les groupes de machines virtuelles identiques ?](../virtual-machine-scale-sets/overview.md)
