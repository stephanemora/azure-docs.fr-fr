---
title: Exemples de script Azure CLI - Créer une machine virtuelle à partir d’une capture instantanée
description: Exemples de script Azure CLI - Créer une machine virtuelle à partir d’une capture instantanée
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 92f56ef06583bbd59509337352e1509b35b449a6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032600"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Créer une machine virtuelle à partir d’une capture instantanée avec l’interface de ligne de commande

Ce script crée une machine virtuelle à partir d’une capture instantanée d’un disque de système d’exploitation.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un disque géré, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Obtient une capture instantanée à l’aide d’un nom de capture instantanée et d’un nom de groupe de ressources. La propriété Id de l’objet retourné est utilisée pour créer un disque géré.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Crée des disques managés à partir d’une capture instantanée en utilisant un ID de capture instantanée, un nom de disque, un type de stockage et une taille  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Crée une machine virtuelle à l’aide d’un disque de système d’exploitation géré |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
