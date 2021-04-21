---
title: Créer un réseau virtuel - Démarrage rapide - Azure CLI
titlesuffix: Azure Virtual Network
description: Dans ce démarrage rapide, découvrez comment créer un réseau virtuel à l’aide d’Azure CLI. Un réseau virtuel permet à des ressources Azure de communiquer entre elles et avec Internet.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776750"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Démarrage rapide : Créer un réseau virtuel à l’aide d’Azure CLI

Un réseau virtuel permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé entre elles et avec Internet. 

Dans ce guide de démarrage rapide, vous allez apprendre à créer un réseau virtuel. Après avoir créé un réseau virtuel, déployez deux machines virtuelles dans le réseau virtuel. Vous vous connectez alors aux machines virtuelles depuis Internet et vous communiquez en privé sur le nouveau réseau virtuel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ce guide de démarrage rapide nécessite la version 2.0.28 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Créer un groupe de ressources et un réseau virtuel

Avant de pouvoir créer un réseau virtuel, vous devez créer un groupe de ressources qui hébergera le réseau virtuel. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Cet exemple crée un groupe de ressources nommé **CreateVNetQS-rg** dans la région **Eastus** :

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Cet exemple crée un réseau virtuel par défaut nommé **myVNet** avec un sous-réseau nommé **default** :

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel.

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). 

Si des clés SSH n’existent pas déjà dans un emplacement de clé par défaut, la commande les crée. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`. 

L’option `--no-wait` permet de créer la machine virtuelle en arrière-plan. Vous pouvez passer à l’étape suivante. 

Cet exemple crée une machine virtuelle nommée **myVM1** :

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

Vous avez utilisé l’option `--no-wait` à l’étape précédente. Vous pouvez continuer et créer la deuxième machine virtuelle nommée **myVM2**.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Message de sortie Azure CLI

La création des machines virtuelles peut prendre plusieurs minutes. Une fois les machines virtuelles créées, Azure CLI renvoie une sortie comme la suivante :

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>IP publique de machine virtuelle

Pour obtenir l’IP publique de **myVM2**, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) :

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Dans cette commande, remplacez `<publicIpAddress>` par l’IP publique de votre machine virtuelle **myVM2** :

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

Pour vérifier la communication privée entre les machines virtuelles **myVM2** et **myVM1**, entrez cette commande :

```bash
ping myVM1 -c 4
```

Vous recevrez quatre réponses de *10.0.0.4*.

Quittez la session SSH avec la machine virtuelle **myVM2**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n'en avez plus besoin, vous pouvez utiliser [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, ainsi que toutes les ressources qu’il contient :

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide : 

* Vous avez créé un réseau virtuel par défaut et deux machines virtuelles. 
* Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez établi une communication privée entre les deux machines virtuelles.

La communication privée entre les machines virtuelles n’est pas limitée dans un réseau virtuel. 

Passez à l'article suivant pour en savoir plus sur la configuration de différents types de communications réseau de machines virtuelles :
> [!div class="nextstepaction"]
> [Filtrer le trafic réseau](tutorial-filter-network-traffic.md)
