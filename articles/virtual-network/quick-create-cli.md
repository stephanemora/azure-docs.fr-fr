---
title: Créer un réseau virtuel - Démarrage rapide - Azure CLI | Microsoft Docs
description: Dans ce démarrage rapide, vous découvrez comment créer un réseau virtuel à l’aide du portail Azure. Un réseau virtuel permet à des ressources Azure, par exemple des machines virtuelles, de communiquer en privé entre elles et avec Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: bb45b2b4ecd89187e94066bc81782174738fe3a9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Démarrage rapide : Créer un réseau virtuel à l’aide d’Azure CLI

Un réseau virtuel permet à des ressources Azure, par exemple des machines virtuelles, de communiquer en privé entre elles et avec Internet. Dans ce guide de démarrage rapide, vous allez apprendre à créer un réseau virtuel. Après avoir créé un réseau virtuel, déployez deux machines virtuelles dans le réseau virtuel. Vous vous connectez alors à une machine virtuelle à partir d’internet et vous communiquer de manière privée avec les autres machines virtuelles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite que vous exécutiez la version 2.0.28 d’Azure CLI, ou une version ultérieure. Pour trouver la version installée, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Avant de pouvoir créer un réseau virtuel, vous devez créer un groupe de ressources pour qu’il contienne le réseau virtuel. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L’exemple suivant crée un réseau virtuel par défaut nommé *myVirtualNetwork* avec un sous-réseau nommé *default* :

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel :

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). Si des clés SSH n’existent pas déjà dans un emplacement de clé par défaut, la commande les crée. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`. L’option `--no-wait` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante. L’exemple suivant crée une machine virtuelle nommée *myVm1* :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Créer la deuxième machine virtuelle

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La création de la machine virtuelle ne nécessite que quelques minutes. Une fois la machine virtuelle créée, Azure CLI retourne une sortie similaire à celle-ci : 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Veuillez noter **publicIpAddress**. Cette adresse est utilisée pour se connecter à la machine virtuelle à partir d’Internet à l’étape suivante.

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Remplacez `<publicIpAddress>` par l’adresse IP publique de votre machine virtuelle *myVm2* dans la commande qui suit, puis entrez la commande suivante :

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

Pour vérifier la communication privée entre les machines virtuelles *myVm2* et *myVm1*, entrez la commande suivante :

```bash
ping myVm1 -c 4
```

Vous recevez quatre réponses de *10.0.0.4*.

Fermez la session SSH avec la machine virtuelle *myVm2*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, vous pouvez utiliser [az group delete](/cli/azure/group#az_group_delete) pour le supprimer, ainsi que toutes les ressources qu’il contient :

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un réseau virtuel par défaut et deux machines virtuelles. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez établi une communication privée entre deux machines virtuelles. Pour plus d’informations sur les paramètres des réseaux virtuels, consultez [Gérer un réseau virtuel](manage-virtual-network.md). 

Par défaut, Azure autorise une communication privée illimitée entre des machines virtuelles, mais autorise uniquement les connexions Bureau à distance entrantes pour les machines virtuelles Windows à partir d’Internet. Pour découvrir comment autoriser ou limiter les différents types de communication réseau vers et depuis les machines virtuelles, consultez [Filtrer le trafic réseau](tutorial-filter-network-traffic.md).
