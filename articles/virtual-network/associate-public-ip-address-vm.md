---
title: Associer une adresse IP publique à une machine virtuelle
titlesuffix: Azure Virtual Network
description: Découvrez comment associer une adresse IP publique à une machine virtuelle.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 69460a111e6fd879807b4025d6832b3ac515a9b4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691980"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associer une adresse IP publique à une machine virtuelle

Dans cet article, vous allez apprendre à associer une adresse IP publique à une machine virtuelle (VM). Si vous souhaitez vous connecter à une machine virtuelle à partir d’internet, la machine virtuelle doit avoir une adresse IP publique qui lui sont associée. Si vous souhaitez créer une machine virtuelle avec une adresse IP publique, vous pouvez le faire avec le [Azure portal](virtual-network-deploy-static-pip-arm-portal.md), le [interface de ligne de commande Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md), ou [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Les adresses IP publiques ont un coût nominal. Pour en savoir plus, consultez les [tarifs](https://azure.microsoft.com/pricing/details/ip-addresses/). Il existe une limite au nombre d’adresses IP publiques que vous pouvez utiliser par abonnement. Pour plus d’informations, consultez [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Vous pouvez utiliser la [Azure portal](#azure-portal), Azure [interface de ligne de](#azure-cli) (CLI), ou [PowerShell](#powershell) pour associer une adresse IP publique à une machine virtuelle.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à, ou recherchez la machine virtuelle que vous souhaitez ajouter l’adresse IP publique, puis sélectionnez-le.
3. Sous **paramètres**, sélectionnez **mise en réseau**, puis sélectionnez l’interface réseau que vous souhaitez ajouter l’adresse IP publique, comme illustré dans l’image suivante :

   ![Sélectionner une interface réseau](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Les adresses IP publiques sont associés à des interfaces réseau attachées à une machine virtuelle. Dans l’image précédente, la machine virtuelle a uniquement une interface réseau. Si la machine virtuelle a plusieurs interfaces réseau, ils doivent tous apparaître et vous sélectionnez l’interface réseau que vous souhaitez associer l’adresse IP publique.

4. Sélectionnez **configurations IP** puis sélectionnez une configuration IP, comme indiqué dans l’image suivante :

   ![Sélectionner la configuration IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Les adresses IP publiques sont associés à des configurations IP pour une interface réseau. Dans l’image précédente, l’interface réseau a une configuration IP. Si l’interface réseau dispose de plusieurs configurations IP, ils doivent tous apparaître dans la liste, et vous sélectionneriez la configuration IP que vous souhaitez associer l’adresse IP publique.

5. Sélectionnez **activé**, puis sélectionnez **adresse IP (*configurer les paramètres requis*)**. Choisir une adresse IP publique existante, qui ferme automatiquement le **choisir une adresse IP publique** boîte. Si vous n’avez pas d’adresses IP publiques disponibles répertoriés, vous devez en créer un. Pour savoir comment procéder, consultez [créer une adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address). Sélectionnez **enregistrer**, comme illustré dans l’image qui suit, puis fermez la boîte pour la configuration IP.

   ![Activer l’adresse IP publique](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Les adresses IP publiques qui apparaissent sont ceux qui existent dans la même région que la machine virtuelle. Si vous avez plusieurs adresses IP publiques créées dans la région, tous apparaissent ici. Si un sont grisé, il est, car l’adresse est déjà associé à une autre ressource.

6. Affichez l’adresse IP publique affectée à la configuration IP, comme indiqué dans l’image qui suit. Il peut prendre quelques secondes pour une adresse IP s’affichent.

   ![Vue d’adresse IP publique](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > L’adresse est attribuée à partir d’un pool d’adresses utilisées dans chaque région Azure. Pour obtenir une liste des pools d’adresses utilisées dans chaque région, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). L’adresse attribuée peut être n’importe quelle adresse dans les pools utilisé pour la région. Si vous avez besoin de l’adresse à affecter à partir d’un pool spécifique dans la région, utilisez un [préfixe d’adresse IP publique](public-ip-address-prefix.md).

7. [Autoriser le trafic réseau à la machine virtuelle](#allow-network-traffic-to-the-vm) avec les règles de sécurité dans un groupe de sécurité réseau.

## <a name="azure-cli"></a>Azure CLI

Installer le [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), ou utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Sélectionnez le **essayez-le** commandes de bouton dans l’interface CLI qui suivent. En sélectionnant **essayez-le** appelle un Cloud Shell avec lequel vous pouvez vous connecter à votre compte Azure avec.

1. Si vous utilisez l’interface CLI localement dans Bash, connectez-vous à Azure avec `az login`.
2. Une adresse IP publique est associée à une configuration IP d’une interface réseau attachée à une machine virtuelle. Utilisez le [mise à jour de az réseau nic-ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) commande à associer une adresse IP publique à une configuration IP. L’exemple suivant associe une adresse IP publique existante nommée *myVMPublicIP* à la configuration IP nommée *ipconfigmyVM* d’une interface réseau existante nommée *myVMVMNic* qui existe dans un groupe de ressources nommé *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Si vous n’avez pas une adresse IP publique existante, utilisez le [créer az network public-ip](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) commande pour créer un. Par exemple, la commande suivante crée une adresse IP publique nommée *myVMPublicIP* dans un groupe de ressources nommé *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > La commande précédente crée une adresse IP publique avec les valeurs par défaut pour plusieurs paramètres que vous pouvez personnaliser. Pour en savoir plus sur tous les paramètres d’adresse IP publiques, consultez [créer une adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address). L’adresse est affectée d’un pool d’adresses IP publiques utilisées pour chaque région Azure. Pour obtenir une liste des pools d’adresses utilisées dans chaque région, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

   - Si vous ne connaissez pas le nom d’une interface réseau attachée à votre machine virtuelle, utilisez le [liste de carte réseau de machine virtuelle az](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) commande pour les afficher. Par exemple, la commande suivante répertorie les noms des interfaces réseau attachées à une machine virtuelle nommée *myVM* dans un groupe de ressources nommé *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     La sortie inclut une ou plusieurs lignes qui sont similaires à l’exemple suivant :
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Dans l’exemple précédent, *myVMVMNic* est le nom de l’interface réseau.

   - Si vous ne connaissez pas le nom d’une configuration IP pour une interface réseau, utilisez le [liste de az network nic ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) commande pour les récupérer. Par exemple, la commande suivante répertorie les noms des configurations IP d’une interface réseau nommée *myVMVMNic* dans un groupe de ressources nommé *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Afficher l’adresse IP publique affectée à la configuration IP avec le [az vm list-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) commande. L’exemple suivant montre les adresses IP assignées à une machine virtuelle existante nommée *myVM* dans un groupe de ressources nommé *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > L’adresse est attribuée à partir d’un pool d’adresses utilisées dans chaque région Azure. Pour obtenir une liste des pools d’adresses utilisées dans chaque région, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). L’adresse attribuée peut être n’importe quelle adresse dans les pools utilisé pour la région. Si vous avez besoin de l’adresse à affecter à partir d’un pool spécifique dans la région, utilisez un [préfixe d’adresse IP publique](public-ip-address-prefix.md).

4. [Autoriser le trafic réseau à la machine virtuelle](#allow-network-traffic-to-the-vm) avec les règles de sécurité dans un groupe de sécurité réseau.

## <a name="powershell"></a>PowerShell

Installer [PowerShell](/powershell/azure/install-az-ps), ou utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes gratuit que vous pouvez exécuter directement dans le portail Azure. PowerShell y est préinstallé et configuré pour être utilisé avec votre compte. Sélectionnez le **essayez-le** commandes de bouton dans la commande PowerShell qui suivent. En sélectionnant **essayez-le** appelle un Cloud Shell avec lequel vous pouvez vous connecter à votre compte Azure avec.

1. Si vous utilisez PowerShell localement, connectez-vous à Azure avec `Connect-AzAccount`.
2. Une adresse IP publique est associée à une configuration IP d’une interface réseau attachée à une machine virtuelle. Utilisez le [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) et [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) commandes pour obtenir le réseau virtuel et un sous-réseau figurant dans l’interface réseau. Ensuite, utilisez le [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) commande pour obtenir une interface réseau et le [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) commande pour obtenir une adresse IP publique existante. Puis utiliser le [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) commande pour associer l’adresse IP publique à la configuration IP et le [AzNetworkInterface de jeu](/powershell/module/Az.Network/Set-AzNetworkInterface) commande pour écrire la nouvelle configuration IP à la interface réseau.

   L’exemple suivant associe une adresse IP publique existante nommée *myVMPublicIP* à la configuration IP nommée *ipconfigmyVM* d’une interface réseau existante nommée *myVMVMNic* qui existe dans un sous-réseau nommé *myVMSubnet* dans un réseau virtuel nommé *myVMVNet*. Toutes les ressources se trouvent dans un groupe de ressources nommé *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Si vous n’avez pas une adresse IP publique existante, utilisez le [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) commande pour créer un. Par exemple, la commande suivante crée un *dynamique* adresse IP publique nommée *myVMPublicIP* dans un groupe de ressources nommé *myResourceGroup* dans le  *eastus* région.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > La commande précédente crée une adresse IP publique avec les valeurs par défaut pour plusieurs paramètres que vous pouvez personnaliser. Pour en savoir plus sur tous les paramètres d’adresse IP publiques, consultez [créer une adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address). L’adresse est affectée d’un pool d’adresses IP publiques utilisées pour chaque région Azure. Pour obtenir une liste des pools d’adresses utilisées dans chaque région, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

   - Si vous ne connaissez pas le nom d’une interface réseau attachée à votre machine virtuelle, utilisez le [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) commande pour les afficher. Par exemple, la commande suivante répertorie les noms des interfaces réseau attachées à une machine virtuelle nommée *myVM* dans un groupe de ressources nommé *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     La sortie inclut une ou plusieurs lignes qui sont similaires à l’exemple qui suit. Dans l’exemple de sortie, *myVMVMNic* est le nom de l’interface réseau.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Si vous ne connaissez pas le nom du réseau virtuel ou sous-réseau figurant dans l’interface réseau, utilisez le `Get-AzNetworkInterface` commande pour afficher les informations. Par exemple, la commande suivante obtient les informations de sous-réseau et le réseau virtuelles pour une interface réseau nommée *myVMVMNic* dans un groupe de ressources nommé *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     La sortie inclut une ou plusieurs lignes qui sont similaires à l’exemple qui suit. Dans l’exemple de sortie, *myVMVNET* est le nom du réseau virtuel et *myVMSubnet* est le nom du sous-réseau.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Si vous ne connaissez pas le nom d’une configuration IP pour une interface réseau, utilisez le [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) commande pour les récupérer. Par exemple, la commande suivante répertorie les noms des configurations IP d’une interface réseau nommée *myVMVMNic* dans un groupe de ressources nommé *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     La sortie inclut une ou plusieurs lignes qui sont similaires à l’exemple qui suit. Dans l’exemple de sortie, *ipconfigmyVM* est le nom d’une configuration IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Afficher l’adresse IP publique affectée à la configuration IP avec le [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) commande. L’exemple suivant montre l’adresse affectée à une adresse IP publique nommée *myVMPublicIP* dans un groupe de ressources nommé *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Si vous ne connaissez pas le nom de l’adresse IP publique affectée à une configuration IP, exécutez les commandes suivantes pour l’obtenir :

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   La sortie inclut une ou plusieurs lignes qui sont similaires à l’exemple qui suit. Dans l’exemple de sortie, *myVMPublicIP* est le nom de l’adresse IP publique affectée à la configuration IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > L’adresse est attribuée à partir d’un pool d’adresses utilisées dans chaque région Azure. Pour obtenir une liste des pools d’adresses utilisées dans chaque région, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). L’adresse attribuée peut être n’importe quelle adresse dans les pools utilisé pour la région. Si vous avez besoin de l’adresse à affecter à partir d’un pool spécifique dans la région, utilisez un [préfixe d’adresse IP publique](public-ip-address-prefix.md).

4. [Autoriser le trafic réseau à la machine virtuelle](#allow-network-traffic-to-the-vm) avec les règles de sécurité dans un groupe de sécurité réseau.

## <a name="allow-network-traffic-to-the-vm"></a>Autoriser le trafic réseau à la machine virtuelle

Avant de vous connecter à l’adresse IP publique à partir d’internet, assurez-vous que les ports nécessaires ouvert dans n’importe quel groupe de sécurité réseau associées à l’interface réseau, le sous-réseau de que l’interface réseau ou les deux. Bien que les groupes de sécurité filtrer le trafic vers l’adresse IP privée de l’interface réseau, une fois que le trafic internet entrant arrive à l’adresse IP publique, Azure traduit l’adresse publique à l’adresse IP privée d’adresses, par conséquent si un groupe de sécurité réseau empêche la le flux de trafic, la communication avec l’adresse IP publique échoue. Vous pouvez afficher les règles de sécurité effectives pour une interface réseau et de son sous-réseau à l’aide de la [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), ou [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Étapes suivantes

Autoriser le trafic internet entrant à votre machine virtuelle avec un groupe de sécurité réseau. Pour savoir comment créer un groupe de sécurité réseau, consultez [fonctionne avec les groupes de sécurité réseau](manage-network-security-group.md#work-with-network-security-groups). Pour en savoir plus sur les groupes de sécurité réseau, consultez [groupes de sécurité](security-overview.md).
