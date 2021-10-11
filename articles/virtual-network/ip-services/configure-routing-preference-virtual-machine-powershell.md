---
title: 'Tutoriel : Configurer la préférence de routage pour une machine virtuelle – Azure PowerShell'
description: Dans ce tutoriel, apprenez à créer une machine virtuelle avec une adresse IP publique dotée d’un choix de préférence de routage, à l’aide d’Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5442bbd177feab409daa7ed5eb5bad95aeed9e04
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368919"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-azure-powershell"></a>Tutoriel : Configurer la préférence de routage pour une machine virtuelle à l’aide d’Azure PowerShell

Ce tutoriel explique comment configurer la préférence de routage pour une machine virtuelle. Le trafic lié à Internet à partir de la machine virtuelle est routé via le réseau du fournisseur de services Internet lorsque vous choisissez l’option de préférence de routage **Internet**. Le routage par défaut passe par le réseau Microsoft mondial.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une adresse IP publique configurée pour la préférence de routage **Internet**.
> * Création d’une machine virtuelle
> * Vérifier que la préférence de routage de l’adresse IP publique est définie sur **Internet**.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installé localement ou Azure Cloud Shell

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), créez un groupe de ressources nommé **TutorVMRoutePref-rg** dans l’emplacement **westus2**.

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorVMRoutePref-rg' -Location 'westus2'

```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) pour créer une adresse IPv4 publique redondante interzone standard, nommée **myPublicIP**, dans **TutorVMRoutePref-rg**. L’**étiquette** de **Internet** est appliquée à l’adresse IP publique sous forme de paramètre dans la commande PowerShell, ce qui active la préférence de routage **Internet**.

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'westus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Utilisez [New-AzVM](/powershell/module/az.compute/new-azvm) pour créer une machine virtuelle. L’adresse IP publique créée à la section précédente est ajoutée à la commande PowerShell et attachée à la machine virtuelle lors de la création.

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'West US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="verify-internet-routing-preference"></a>Vérifier la préférence de routage Internet

Utilisez [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) afin de vérifier que la préférence de routage **Internet** est configurée pour l’adresse IP publique.

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select -ExpandProperty IpTags

```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’avez plus besoin de la machines virtuelle ni de l’adresse IP publique, supprimez le groupe de ressources et toutes les ressources qu’il contient à l’aide de [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer une machine virtuelle avec une préférence de routage mixte :
> [!div class="nextstepaction"]
> [Configurer les deux options de préférence de routage pour une machine virtuelle](routing-preference-mixed-network-adapter-portal.md)

