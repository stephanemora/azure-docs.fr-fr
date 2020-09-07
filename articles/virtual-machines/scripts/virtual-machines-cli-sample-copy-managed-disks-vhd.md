---
title: Copier un disque managé dans un compte de stockage - CLI
description: Exemple Azure CLI - Exporter ou copier un disque managé dans un compte de stockage
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/09/2019
ms.author: ramankum
ms.custom: mvc,seodec18, devx-track-azurecli
ms.openlocfilehash: c43a18f1dcb4122eb6c1407ca11b7c60653594c4
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322747"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Exporter/copier un disque managé vers un compte de stockage à l’aide d’Azure CLI

Ce script permet d’exporter le VHD sous-jacent d’un disque managé vers un compte de stockage situé dans la même région ou dans une autre région. Il génère d’abord l’URI SAS du disque managé, puis l’utilise pour copier le VHD vers un compte de stockage. Utilisez ce script pour copier des disques managés vers une autre région en vue d’une extension régionale. Si vous souhaitez publier le fichier VHD d’un disque managé sur la Place de marché Azure, vous pouvez utiliser ce script pour copier le fichier VHD vers un compte de stockage, puis générer l’URI SAS du VHD copié à publier sur la Place de marché.   


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes afin de générer l’URI SAS pour un disque managé et copie le VHD sous-jacent vers un compte de stockage à l’aide de l’URI SAS. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az disk grant-access](/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Génère l’URI SAP en lecture seule qui est utilisé pour copier le fichier de VHD sous-jacent vers un compte de stockage ou le télécharger en local  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | Copie un objet blob de façon asynchrone à partir d’un compte de stockage vers un autre. |

## <a name="next-steps"></a>Étapes suivantes

[Créer un disque managé à partir d’un VHD](virtual-machines-cli-sample-create-managed-disk-from-vhd.md)

[Créer une machine virtuelle à partir d’un disque géré](virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md)

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle et de disques managés dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md).
