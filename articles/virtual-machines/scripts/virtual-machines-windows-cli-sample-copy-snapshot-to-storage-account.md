---
title: Copier une capture instantanée dans un compte de stockage appartenant à une autre région - Exemple d’interface de ligne de commande Windows
description: Exemple de Script Azure CLI - Exporter/copier une capture instantanée en tant que VHD vers un compte de stockage dans une région identique ou différente.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 2bad4f5f3bb85f062d4c17eb2d9e77cb51ab2779
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501107"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Exporter/copier une capture instantanée vers un compte de stockage dans une région différente à l’aide de CLI

Ce script exporte une capture instantanée managée vers un compte de stockage dans une région différente. Il génère d’abord l’URI SAP de la capture instantanée et l’utilise ensuite pour copier celle-ci vers un compte de stockage dans une autre région. Ce script vous permet de conserver la sauvegarde de vos disques managés dans une autre région en cas de récupération d’urgence.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes afin de générer un URI SAS pour une capture instantanée gérée et copie cette dernière vers un compte de stockage à l’aide de l’URI SAS. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az snapshot grant-access](/cli/azure/snapshot) | Génère l’URI SAP en lecture seule qui est utilisé pour copier le fichier de VHD sous-jacent vers un compte de stockage ou le télécharger en local.  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | Copie un objet blob de façon asynchrone à partir d’un compte de stockage vers un autre. |

## <a name="next-steps"></a>Étapes suivantes

[Créer un disque managé à partir d’un VHD](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle et de disques managés dans la [documentation relative aux machines virtuelles Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
