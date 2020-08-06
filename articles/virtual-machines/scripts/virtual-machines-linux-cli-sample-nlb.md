---
title: Exemple de script Azure CLI - Créer une machine virtuelle Linux avec équilibrage de charge réseau
description: Exemple de script Azure CLI - Créer une machine virtuelle Linux avec équilibrage de charge réseau
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
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 15d5a11d91a2ffcea98fd8a3f5c7bc3a08f32613
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479521"
---
# <a name="create-a-highly-available-vm"></a>Créer une machine virtuelle hautement disponible

Cet exemple de script crée tous les éléments nécessaires pour exécuter plusieurs machines virtuelles Ubuntu configurées dans une configuration haute disponibilité avec équilibrage de la charge. Une fois que vous avez exécuté le script, vous obtenez trois machines virtuelles jointes à un groupe à haute disponibilité Azure et accessibles par le biais d’Azure Load Balancer.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle, un groupe à haute disponibilité, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](/cli/azure/network/vnet) | Crée un réseau virtuel et un sous-réseau Azure. |
| [az network public-ip create](/cli/azure/network/public-ip) | Crée une adresse IP publique avec une adresse IP statique et un nom DNS associé. |
| [az network lb create](/cli/azure/network/lb) | Crée un équilibreur de charge réseau Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe) | Crée une sonde d’équilibrage de charge réseau. Une sonde d’équilibrage de charge réseau permet de surveiller chaque machine virtuelle dans le jeu d’équilibrage de charge réseau. Si une machine virtuelle n’est plus accessible, le trafic n’est pas acheminé vers cette machine virtuelle. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Crée une règle d’équilibrage de charge réseau. Dans cet exemple, une règle est créée pour le port 80. Comme le trafic HTTP arrive au niveau de l’équilibrage de charge réseau, il est acheminé vers le port 80 de l’une des machines virtuelles du jeu d’équilibrage de charge réseau. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) | Crée une règle de traduction d’adresses réseau (NAT) de l’équilibrage de charge réseau.  Les règles NAT mappent un port de l’équilibrage de charge réseau à un port de machine virtuelle. Dans cet exemple, une règle NAT est créée pour le trafic SSH en direction de chaque machine virtuelle du jeu d’équilibrage de charge réseau.  |
| [az network nsg create](/cli/azure/network/nsg) | Crée un groupe de sécurité réseau qui représente une frontière de sécurité entre Internet et la machine virtuelle. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Crée une règle de groupe de sécurité réseau permettant d’autoriser le trafic entrant. Dans cet exemple, le port 22 est ouvert pour le trafic SSH. |
| [az network nic create](/cli/azure/network/nic) | Crée une carte réseau virtuelle et l’associe au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. |
| [az vm availability-set create](/cli/azure/network/lb/rule) | Crée un groupe à haute disponibilité. Les groupes à haute disponibilité garantissent le temps de fonctionnement des applications en répartissant les machines virtuelles sur les ressources physiques de sorte que, en cas d’échec, l’ensemble du groupe ne soit pas affecté. |
| [az vm create](/cli/azure/vm/availability-set) | Crée la machine virtuelle et l’associe à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az group delete](/cli/azure/vm/extension) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
