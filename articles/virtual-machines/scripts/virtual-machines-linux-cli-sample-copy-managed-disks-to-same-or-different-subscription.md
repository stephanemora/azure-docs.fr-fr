---
title: Copier les disques managés dans un abonnement - Exemple CLI
description: Exemple de script Azure CLI - Copier (ou déplacer) des disques managés dans un même abonnement ou dans un abonnement différent
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7ccdec703781a4e48870d5b4c27ec9d23de71dd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75458579"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copier des disques managés vers un abonnement identique ou différent avec l’interface de ligne de commande

Ce script copie le disque managé vers un abonnement identique ou différent, mais dans la même région. La copie ne fonctionne que si les abonnements font partie du même locataire AAD.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un disque géré dans l’abonnement cible à l’aide de l’ID du disque source géré. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Obtient toutes les propriétés d’un disque managé en utilisant les propriétés de nom et de groupe de ressources du disque managé. La propriété de l’identifiant est utilisée pour copier le disque managé vers un autre abonnement.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Copie un disque managé en créant un disque managé dans un autre abonnement à l’aide de l’identifiant et du nom du disque managé parent.  |

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle à partir d’un disque géré](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle et de disques managés dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
