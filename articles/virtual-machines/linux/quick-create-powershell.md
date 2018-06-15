---
title: 'Démarrage rapide : créer une machine virtuelle Linux avec Azure PowerShell | Microsoft Docs'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Azure PowerShell pour créer une machine virtuelle Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e910ced35738fcba27a8a1d7f2f010b2cd42e55d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188988"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Démarrage rapide : créer une machine virtuelle Linux dans Azure avec PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Ce guide de démarrage rapide explique comment utiliser le module Azure PowerShell pour déployer dans Azure une machine virtuelle Linux qui fonctionne sous Ubuntu. Pour voir votre machine virtuelle en action, vous établirez une connexion SSH à la machine virtuelle et installerez le serveur web NGINX.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.7.0 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

Enfin, une clé SSH publique portant le nom *id_rsa.pub* doit être stockée dans le répertoire *.ssh* de votre profil utilisateur Windows. Pour plus d’informations sur la création et l’utilisation de clés SSH, consultez [Procédure détaillée de création d’une paire de clés SSH et de certificats supplémentaires pour une machine virtuelle Linux dans Azure](ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées :

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Créer des ressources de réseau virtuel

Créez un réseau virtuel, un sous-réseau et une adresse IP publique. Ces ressources sont utilisées pour fournir une connectivité réseau à la machine virtuelle et la connecter à Internet :

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Créez un groupe de sécurité réseau Azure et une règle de trafic. Le groupe de sécurité réseau sécurise la machine virtuelle avec des règles entrantes et sortantes. Dans l’exemple suivant, une règle de trafic entrant est créée pour le port TCP 22 qui autorise les connexions SSH. Pour autoriser le trafic web entrant, une règle de trafic entrant pour le port TCP 80 est également créée.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Créez une interface réseau virtuel avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). La carte réseau virtuelle connecte la machine virtuelle à un sous-réseau, un groupe de sécurité réseau et une adresse IP publique.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

La configuration d’une machine virtuelle inclut les paramètres qui sont utilisés lorsqu’une machine virtuelle est déployée, comme une image de machine virtuelle, la taille et des options d’authentification. Définissez les informations d’identification SSH, les informations du système d’exploitation et la taille de machine virtuelle comme suit :

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Combinez maintenant les définitions de configuration précédentes à créer avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) :

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, établissez une connexion SSH vers la machine virtuelle. Pour que vous puissiez voir votre machine virtuelle en action, le serveur web NGINX est installé.

Pour obtenir l’adresse IP publique de la machine virtuelle, utilisez la cmdlet [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Utilisez un client SSH pour vous connecter à la machine virtuelle. Vous pouvez utiliser Azure Cloud Shell depuis un navigateur web ou, si vous utilisez Windows, vous pouvez utiliser [Putty](ssh-from-windows.md) ou le [sous-système Windows pour Linux](/windows/wsl/install-win10). Fournissez l’adresse IP publique de votre machine virtuelle :

```bash
ssh azureuser@IpAddress
```

Lorsque vous y êtes invité, le nom d’utilisateur à saisir est *azureuser*. Si une phrase secrète est utilisée avec vos clés SSH, vous devez la saisir que lorsque vous y êtes invité.

## <a name="install-web-server"></a>Installer le serveur web

Pour voir votre machine virtuelle en action, installez le serveur web NGINX. Pour mettre à jour les sources du package et installer la dernière version du package NGINX, exécutez les commandes suivantes dans votre session SSH :

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Une fois cette opération terminée, `exit` la session SSH

## <a name="view-the-web-server-in-action"></a>Voir le serveur web en action

Une fois NGINX installé et le port 80 ouvert sur votre machine virtuelle à partir d’Internet, utilisez un navigateur web de votre choix pour afficher la page d’accueil NGINX par défaut. Utilisez l’adresse IP publique de votre machine virtuelle que vous avez obtenue précédemment. L’exemple suivant montre le site web NGINX par défaut :

![Site par défaut NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple, créé un Groupe de sécurité réseau et une règle, et installé un serveur web de base. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Linux.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Linux](./tutorial-manage-vm.md)
