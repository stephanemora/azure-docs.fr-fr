---
title: 'Exemple de script d’interface de ligne de commande Azure : exporter/copier le VHD sous-jacent des disques managés vers un compte de stockage | Microsoft Docs'
description: 'Exemple de script d’interface de ligne de commande Azure : exporter/copier le VHD sous-jacent des disques managés vers un compte de stockage'
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
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: c5f06a8c8fb707a2bf0451f8e9ed391ac0c5bad9
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045240"
---
# <a name="exportcopy-the-underlying-vhd-of-a-managed-disk-to-a-storage-account-with-cli"></a>Exporter/copier le VHD sous-jacent d’un disque managé vers un compte de stockage avec l’interface CLI

Ce script permet d’exporter le VHD sous-jacent d’un disque managé vers un compte de stockage situé dans la même région ou dans une autre région. Il génère d’abord l’URI SAS du disque managé, puis l’utilise pour copier le VHD vers un compte de stockage. Utilisez ce script pour copier vos disques managés en vue d’une extension régionale. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes afin de générer l’URI SAS pour un disque managé et copie le VHD sous-jacent vers un compte de stockage à l’aide de l’URI SAS. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Génère l’URI SAP en lecture seule qui est utilisé pour copier le fichier de VHD sous-jacent vers un compte de stockage ou le télécharger en local  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Copie un objet blob de façon asynchrone à partir d’un compte de stockage vers un autre. |

## <a name="next-steps"></a>Étapes suivantes

[Créer un disque managé à partir d’un VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Créer une machine virtuelle à partir d’un disque géré](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle et de disques managés dans la [documentation relative aux machines virtuelles Linux Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
