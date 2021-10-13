---
title: Créer une machine virtuelle avec une adresse IP privée statique - Azure CLI
description: Découvrez comment créer une machine virtuelle avec une adresse IP privée statique à l’aide d’Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 900010fd454e356b43eb7cb5b2ee0379bdf90beb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368286"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-cli"></a>Créer une machine virtuelle avec une adresse IP privée statique à l’aide d’Azure CLI

Une adresse IP privée est automatiquement assignée à une machine virtuelle (VM) Azure, dans une plage que vous définissez. Cette plage est basée sur le sous-réseau dans lequel la machine virtuelle est déployée. Cette adresse est conservée jusqu’à ce que la machine virtuelle soit supprimée. Azure attribue dynamiquement l’adresse IP privée disponible suivante du sous-réseau dans lequel vous créez une machine virtuelle. Assignez une adresse IP statique à la machine virtuelle si vous souhaitez assigner une adresse IP spécifique du sous-réseau.

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

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec [az vm create](/cli/azure/vm#az_vm_create). 

La commande suivante crée une machine virtuelle Windows Server 2016. Quand vous y êtes invité, indiquez le nom d’utilisateur et le mot de passe à utiliser comme informations d’identification pour la machine virtuelle :

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group myResourceGroup \
    --public-ip-address myPublicIP \
    --public-ip-sku Standard \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

## <a name="change-private-ip-address-to-static"></a>Passer une adresse IP privée en statique

Dans cette section, vous allez faire passer l’adresse IP privée de **dynamique** à **statique** pour la machine virtuelle que vous avez créée précédemment. 

Utilisez [az network nic ip-config update](/cli/azure/network/nic/ipconfig#az_network_nic_ip_config_update) pour mettre à jour la configuration de l’interface réseau.

La commande suivante remplace l’adresse IP privée de la machine virtuelle par une adresse statique :

```azurecli-interactive
  az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --private-ip-address 10.0.0.4
```

> [!WARNING]
> Même si vous avez la possibilité d’ajouter des paramètres d’adresse IP privée au système d’exploitation, nous vous déconseillons de le faire avant d’avoir lu [Add a private IP address to an operating system](virtual-network-network-interface-addresses.md#private) (Ajouter une adresse IP privée à un système d’exploitation).

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