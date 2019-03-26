---
title: Exemple de script Azure CLI – Création d’une machine virtuelle Linux avec Azure Monitor | Microsoft Docs
description: Exemple de script Azure CLI – Création d’une machine virtuelle Linux avec Azure Monitor
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
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
ms.openlocfilehash: 6aed5f40d689fc84c82f37760e78fc7c3fe94dd1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535619"
---
# <a name="monitor-a-vm-with-azure-monitor"></a>Analyser une machine virtuelle avec Azure Monitor

Ce script crée une machine virtuelle Azure, installe l’agent Log Analytics et inscrit le système auprès d’un espace de travail Log Analytics. Une fois le script exécuté, la machine virtuelle est visible dans la console.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.sh "Quick Create VM")]

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
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Crée la machine virtuelle et l’associe à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension) | Exécute une extension de machine virtuelle sur une machine virtuelle. Dans ce cas, l’extension de l’agent Azure Monitor est utilisée pour installer l’agent Log Analytics et inscrire la machine virtuelle dans un espace de travail Log Analytics. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
