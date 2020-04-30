---
title: 'Exemple CLI : Créer deux machines virtuelles avec un groupe de sécurité réseau interne et un groupe de sécurité réseau externe'
description: Créez deux machines virtuelles avec un groupe de sécurité réseau interne et un groupe de sécurité réseau externe pour sécuriser le trafic réseau à l’aide d’Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b4339ab2c512a96614158f673cb07b60184f5f71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459948"
---
# <a name="secure-network-traffic-between-virtual-machines-using-an-nsg"></a>Sécuriser le trafic réseau entre les machines virtuelles à l’aide d’un groupe de sécurité réseau

Ce script crée deux machines virtuelles et sécurise le trafic entrant vers les deux machines. L’une des machines virtuelles est accessible sur Internet et dispose d’un groupe de sécurité réseau configuré pour autoriser le trafic sur le port 22 et le port 80. La seconde machine virtuelle n’est pas accessible sur Internet et elle dispose d’un groupe de sécurité réseau configuré pour autoriser uniquement le trafic provenant de la première machine virtuelle.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Crée un réseau virtuel et un sous-réseau Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Crée un sous-réseau. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Crée la machine virtuelle et l’associe à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Renvoie les informations relatives à une règle de groupe de sécurité réseau. Dans cet exemple, le nom de la règle est stocké dans une variable à utiliser ultérieurement dans le script. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Met à jour une règle de groupe de sécurité réseau. Dans cet exemple, la règle principale est mise à jour pour transférer le trafic uniquement à partir du sous-réseau frontal. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
