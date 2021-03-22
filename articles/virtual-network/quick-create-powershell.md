---
title: Créer un réseau virtuel - Démarrage rapide - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Dans ce démarrage rapide, vous allez créer un réseau virtuel à l’aide du portail Azure. Un réseau virtuel permet à des ressources Azure de communiquer entre elles et avec Internet.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b27f050d3d37daab05e8c5125d6b75a6bb4dea50
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199031"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Démarrage rapide : Créer un réseau virtuel à l’aide de PowerShell

Un réseau virtuel permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé entre elles et avec Internet. 

Dans ce guide de démarrage rapide, vous allez apprendre à créer un réseau virtuel. Après avoir créé un réseau virtuel, déployez deux machines virtuelles dans le réseau virtuel. Vous vous connectez alors aux machines virtuelles depuis Internet et vous communiquez en privé sur le réseau virtuel.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installé localement ou Azure Cloud Shell

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Créer un groupe de ressources et un réseau virtuel

Vous devez passer par un certain nombre d’étapes pour configurer votre groupe de ressources et votre réseau virtuel.

### <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de pouvoir créer un réseau virtuel, vous devez créer un groupe de ressources qui hébergera le réseau virtuel. Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Cet exemple crée un groupe de ressources nommé **CreateVNetQS-rg** dans la région **Eastus** :

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Cet exemple crée un réseau virtuel par défaut nommé **myVNet** dans la région **EastUS** :

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

Azure déploie des ressources dans un sous-réseau au sein d’un réseau virtuel. C’est pourquoi vous devez créer un sous-réseau. Créez une configuration de sous-réseau nommée **Default** à l’aide de la commande [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) :

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associer le sous-réseau au réseau virtuel

Vous pouvez écrire la configuration du sous-réseau dans le réseau virtuel à l’aide de la commande [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Cette commande crée le sous-réseau :

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel.

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

Créez la première machine virtuelle avec [New-AzVM](/powershell/module/az.compute/new-azvm). Lorsque vous exécutez les commandes suivantes, vous êtes invité à fournir des informations d’identification. Entrez un nom d’utilisateur et un mot de passe pour la machine virtuelle :

```azurepowershell-interactive
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
```

L’option `-AsJob` permet de créer la machine virtuelle en arrière-plan. Vous pouvez passer à l’étape suivante.

Lorsqu’Azure commence la création de la machine virtuelle en arrière-plan, vous obtenez quelque chose de similaire à :

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

Créez la deuxième machine virtuelle à l’aide de cette commande :

```azurepowershell-interactive
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Vous devez créer un autre utilisateur et un autre mot de passe. Il ne faut que quelques minutes à Azure pour créer la machine virtuelle.

> [!IMPORTANT]
> Ne passez pas à l’étape suivante avant qu’Azure ait terminé.  Le processus est terminé quand Azure renvoit une sortie à PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Pour obtenir l’adresse IP publique de la machine virtuelle, utilisez [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress).

Cet exemple renvoie l’adresse IP publique de la machine virtuelle **myVm1** :

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
```

Ouvrez une invite de commandes sur votre ordinateur local. Exécutez la commande `mstsc`. Remplacez `<publicIpAddress>` par l’adresse IP publique renvoyée à l’étape précédente :

> [!NOTE]
> Si vous avez exécuté ces commandes à partir d’une invite PowerShell sur votre ordinateur local et que vous utilisez le module Az PowerShell version 1.0 ou une version ultérieure, vous pouvez continuer sur cette interface.

```cmd
mstsc /v:<publicIpAddress>
```
1. Si vous y êtes invité, sélectionnez **Connexion**.

1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

    > [!NOTE]
    > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Vous allez peut-être recevoir un avertissement de certificat. Si vous en recevez un, sélectionnez **Oui** ou **Continuer**.

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

1. Dans le Bureau à distance de **myVm1**, ouvrez PowerShell.

1. Entrez `ping myVm2`.

    Vous obtenez quelque chose de similaire à :

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Le test Ping a échoué car il utilise le protocole ICMP (Internet Control Message Protocol). Par défaut, le protocole ICMP n’est pas autorisé par votre pare-feu Windows.

1. Pour autoriser **myVm2** à effectuer un test ping **myVm1** entrez la commande suivante dans une étape ultérieure :

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Cette commande autorise le protocole ICMP entrant via le pare-feu Windows.

1. Fermez la connexion Bureau à distance sur **myVm1**.

1. Répétez les étapes décrites dans [Se connecter à une machine virtuelle à partir d’Internet](#connect-to-a-vm-from-the-internet). Cette fois, connectez-vous à **myVm2**.

1. À partir d’une invite de commandes sur la machine virtuelle **myVm2**, entrez `ping myvm1`.

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

    Vous recevez des réponses de **myVm1**, car vous avez autorisé ICMP via le pare-feu Windows sur la machine virtuelle **myVm1** lors d’une étape précédente.

1. Fermez la connexion Bureau à distance sur **myVm2**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous en avez terminé avec le réseau virtuel et les machines virtuelles, utilisez [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient :

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide : 

* Vous avez créé un réseau virtuel par défaut et deux machines virtuelles. 
* Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez établi une communication privée entre les deux machines virtuelles.

La communication privée entre les machines virtuelles n’est pas limitée dans un réseau virtuel. 

Passez à l'article suivant pour en savoir plus sur la configuration de différents types de communications réseau de machines virtuelles :
> [!div class="nextstepaction"]
> [Filtrer le trafic réseau](tutorial-filter-network-traffic.md)
