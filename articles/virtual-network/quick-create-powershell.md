---
title: Créer un réseau virtuel - démarrage rapide - Azure PowerShell | Microsoft Docs
description: Dans ce démarrage rapide, vous découvrez comment créer un réseau virtuel à l’aide du portail Azure. Un réseau virtuel permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé entre elles et avec Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 4e2808df56684b257898f3e03f8e9ca36682063b
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341913"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Démarrage rapide : Créer un réseau virtuel à l’aide de PowerShell

Un réseau virtuel permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé entre elles et avec Internet. Dans ce guide de démarrage rapide, vous allez apprendre à créer un réseau virtuel. Après avoir créé un réseau virtuel, déployez deux machines virtuelles dans le réseau virtuel. Vous vous connectez alors aux machines virtuelles depuis Internet et vous communiquez en privé sur le réseau virtuel.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Si vous décidez d’installer et d’utiliser PowerShell en local, vous devez utiliser le module AzureRM PowerShell version 5.4.1 ou une version ultérieure pour les besoins de ce démarrage rapide. Pour trouver la version installée, exécutez `Get-Module -ListAvailable AzureRM`. Consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps) pour installer et mettre à jour des informations.

Enfin, si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzureRmAccount`. Cette commande crée une connexion avec Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Créer un groupe de ressources et un réseau virtuel

Vous devez passer par un certain nombre d’étapes pour configurer votre groupe de ressources et votre réseau virtuel.

### <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de pouvoir créer un réseau virtuel, vous devez créer un groupe de ressources qui hébergera le réseau virtuel. Créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Cet exemple crée un groupe de ressources nommé *myResourceGroup* dans la région *EastUs* :

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Cet exemple crée un réseau virtuel par défaut nommé *myVirtualNetwork* dans la région *EastUS* :

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

Azure déploie des ressources dans un sous-réseau au sein d’un réseau virtuel. C’est pourquoi vous devez créer un sous-réseau. Créez une configuration de sous-réseau nommée [Default](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) à l’aide de la commande *New-AzureRmVirtualNetworkSubnetConfig* :

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associer le sous-réseau au réseau virtuel

Vous pouvez écrire la configuration du sous-réseau dans le réseau virtuel à l’aide de la commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork). Cette commande crée le sous-réseau :

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel.

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

Créez la première machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Lorsque vous exécutez les commandes suivantes, vous êtes invité à fournir des informations d’identification. Entrez un nom d’utilisateur et un mot de passe pour la machine virtuelle :

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

L’option `-AsJob` permet de créer la machine virtuelle en arrière-plan. Vous pouvez passer à l’étape suivante.

Lorsqu’Azure commence la création de la machine virtuelle en arrière-plan, vous obtenez quelque chose de similaire à :

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM
```

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

Créez la deuxième machine virtuelle à l’aide de cette commande :

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Vous devez créer un autre utilisateur et un autre mot de passe. Il ne faut que quelques minutes à Azure pour créer la machine virtuelle.

> [!IMPORTANT]
> Ne passez pas à l’étape suivante avant qu’Azure ait terminé.  Le processus est terminé quand Azure renvoit une sortie à PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Utilisez [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique d’une machine virtuelle. Cet exemple renvoie l’adresse IP publique de la machine virtuelle *myVm1* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ouvrez une invite de commandes sur votre ordinateur local. Exécutez la commande `mstsc`. Remplacez `<publicIpAddress>` par l’adresse IP publique renvoyée à l’étape précédente :

> [!NOTE]
> Si vous avez exécuté ces commandes à partir d’une invite PowerShell sur votre ordinateur local et que vous êtes sur le module AzureRM PowerShell version 5.4.1 ou une version ultérieure, vous pouvez continuer sur cette interface.

```cmd
mstsc /v:<publicIpAddress>
```

Un fichier *.rdp* (Remote Desktop Protocol) est téléchargé sur votre ordinateur et un Bureau à distance s’ouvre.

1. Si vous y êtes invité, sélectionnez **Connexion**.

1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

    > [!NOTE]
    > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Vous allez peut-être recevoir un avertissement de certificat. Si vous en recevez un, sélectionnez **Oui** ou **Continuer**.

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

1. Dans le Bureau à distance de *myVm1*, ouvrez PowerShell.

1. Entrez `ping myVm2`.

    Vous obtenez quelque chose de similaire à :

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudap
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Le test Ping a échoué car il utilise le protocole ICMP (Internet Control Message Protocol). Par défaut, le protocole ICMP n’est pas autorisé par votre pare-feu Windows.

1. Pour autoriser *myVm2* à effectuer un test ping *myVm1* entrez la commande suivante dans une étape ultérieure :

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Cette commande autorise le protocole ICMP entrant via le pare-feu Windows.

1. Fermez la connexion Bureau à distance sur *myVm1*.

1. Répétez les étapes décrites dans [Se connecter à une machine virtuelle à partir d’Internet](#connect-to-a-vm-from-the-internet). Cette fois, connectez-vous à *myVm2*.

1. À partir d’une invite de commandes sur la machine virtuelle *myVm2*, entrez `ping myvm1`.

    Vous obtenez quelque chose de similaire à :

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Vous recevez des réponses de *myVm1*, car vous avez autorisé ICMP via le pare-feu Windows sur la machine virtuelle *myVm1* lors d’une étape précédente.

1. Fermez la connexion Bureau à distance sur *myVm2*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous en avez terminé avec le réseau virtuel et les machines virtuelles, utilisez [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un réseau virtuel par défaut et deux machines virtuelles. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez établi une communication privée entre deux machines virtuelles. Pour plus d’informations sur les paramètres des réseaux virtuels, consultez [Gérer un réseau virtuel](manage-virtual-network.md).

Azure autorise une communication privée illimitée entre des machines virtuelles. Par défaut, Azure permet uniquement les connexions Bureau à distance entrantes pour les machines virtuelles Windows depuis Internet. Pour en savoir plus sur la configuration des différents types de communications de réseau de machine virtuelle, accédez au didacticiel [Filtrer le trafic réseau](tutorial-filter-network-traffic.md).
