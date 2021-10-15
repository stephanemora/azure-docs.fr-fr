---
title: Créer une machine virtuelle avec une IP publique statique – Azure CLI
titlesuffix: Azure Virtual Network
description: Créez une machine virtuelle avec une IP publique statique à l’aide d’Azure CLI. Les adresses IP publiques statiques sont des adresses qui ne changent jamais.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ea60d6b2784972f2a4e3210ad815c4823a27a19
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368489"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Créer une machine virtuelle avec une adresse IP publique statique à l’aide d’Azure CLI

Dans cet article, vous allez créer une machine virtuelle avec une IP publique statique. Une IP publique vous permet de communiquer avec une machine virtuelle à partir d’Internet. Attribuez une IP publique statique, plutôt qu’une adresse dynamique, pour être certain que l’adresse ne changera jamais. 

Les adresses IP publiques ont un [coût nominal](https://azure.microsoft.com/pricing/details/ip-addresses). Le nombre d’adresses IP publiques que vous pouvez utiliser dans un abonnement est [limité](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ce tutoriel nécessite l’interface Azure CLI version 2.0.28 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Avec la commande [az group create](/cli/azure/group#az_group_create), créez un groupe de ressources nommé **myResourceGroup** dans l’emplacement **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une adresse IPv4 publique standard.

La commande suivante crée une IP publique redondante interzone nommée **myPublicIP** dans **myResourceGroup**.

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec [az vm create](/cli/azure/vm#az_vm_create). 

La commande suivante crée une machine virtuelle Windows Server 2016. Entrez le nom de l’IP publique créée précédemment dans le paramètre **`-PublicIPAddressName`** . Quand vous y êtes invité, indiquez le nom d’utilisateur et le mot de passe à utiliser comme informations d’identification pour la machine virtuelle :

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group TutorVMRoutePref-rg \
    --public-ip-address myPublicIP \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

Pour plus d’informations sur les SKU d’IP publiques, consultez [SKU d’IP publiques](public-ip-addresses.md#sku). Une machine virtuelle peut être ajoutée au pool principal d’un équilibreur de charge Azure. Le SKU de l’IP publique doit correspondre à celui de l’IP publique d’un équilibreur de charge. Pour plus d’informations, consultez la page [Équilibrage de charge Azure](../../load-balancer/skus.md).

Examinez l’IP publique qui a été attribuée et vérifiez qu’elle a été créée en tant qu’adresse statique via [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) :

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress,publicIpAllocationMethod,sku] \
    --output table
```

> [!WARNING]
> Ne modifiez pas les paramètres d’adresse IP dans le système d’exploitation de la machine virtuelle. Le système d’exploitation ne reconnaît pas les adresses IP publiques Azure. Même si vous avez la possibilité d’ajouter des paramètres d’adresse IP privée au système d’exploitation, nous vous déconseillons de le faire, à moins que cela soit nécessaire et pas avant d’avoir lu [Ajouter une adresse IP privée à un système d’exploitation](virtual-network-network-interface-addresses.md#private).

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin d’un groupe de ressources, vous pouvez utiliser [az group delete](/cli/azure/group#az_group_delete) pour le supprimer, ainsi que toutes les ressources qu’il contient :

```azurecli-interactive
  az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [adresses IP publiques](public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).
- En savoir plus sur les [adresses IP privées](private-ip-addresses.md) et l’affectation d’une [adresse IP privée statique](virtual-network-network-interface-addresses.md#add-ip-addresses) à une machine virtuelle Azure.
- En savoir plus sur la création de machines virtuelles [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).